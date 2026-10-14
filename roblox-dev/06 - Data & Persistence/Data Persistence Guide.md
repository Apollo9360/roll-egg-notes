---
title: Data Persistence Guide
category: Data & Persistence
tags: [datastore, persistence, session-locking, profilestore, memorystore, messagingservice]
status: active
source_quality: AUTHORITATIVE (Roblox docs) + HIGH CONFIDENCE (ProfileStore)
last_verified: 2026-10-14
aliases: [DataStore guide, Saving player data, Session locking, Player profiles]
---

# Data Persistence Guide

## Summary

- Load each player's data **once**, into server memory, under **one key** with a **session lock**.
- Read and write the in-memory copy during play.
- Save it periodically, on leave, on shutdown and at purchase checkpoints.
- Use `UpdateAsync`, ordered per-key retries with backoff, schema versioning, and never let default data overwrite real data.
- For new projects, use **ProfileStore** (session locking built in) unless you have a reason to build your own. The checked template is [[Recipe - Player Data with ProfileStore]].

## Which storage?

| Need | Use | Notes |
|---|---|---|
| Player progress, inventory, purchases | **DataStore** (standard) | Permanent. Values up to 4 MB per key. |
| Global leaderboard ranking | **OrderedDataStore** (numbers only), or a MemoryStore sorted map for live boards | |
| Matchmaking queues, live cross-server state, auction houses | **MemoryStore** (queue, sorted map, hash map) | Expires after at most 45 days. Fast. |
| Live-tunable values, feature flags | **Configs** | Read-only in game; deploys over about 5 minutes. |
| API keys and tokens | **Secrets store** | Never put these in a DataStore. |
| Announcements, cross-server events | **MessagingService** | Best effort; delivery is **not guaranteed**. |
| Per-server temporary state | Luau tables | Gone when the server closes. |

Cloud services are only accessible from **server** scripts.

## Official limits

From creator-docs, 2026-10-13. Check the docs again before relying on them for tuning.

| Limit | Value |
|---|---|
| DataStore name, key name, scope | 50 characters each |
| Value size | 4 MB (stored as JSON-encoded text) |
| User metadata | 300 characters total |
| Per-key throughput | Read 25 MB/min, write 4 MB/min (rounded up to the next KB per request) |
| Per-server default request rate (standard and ordered, per request type) | `60 + numPlayers × 40` per minute; configurable with `DataStoreService:SetRateLimitForRequestType()` |
| Throttle queue | 30 requests per queue. When full, requests fail with errors 301–306. |
| Storage | `500 MB + 1 MB × lifetime users`; only the latest version counts (compressed) |
| `UpdateAsync` | Counts against **both** the read and write budgets |
| Open Cloud | Shares the experience budget with game servers |
| `BindToClose` | The server waits **30 s** for bound functions, then shuts down regardless |
| MemoryStore memory quota | `64 KB + 1.2 KB × users` |
| MemoryStore requests | `1000 + 120 × CCU` units per minute |
| One sorted map or queue | Up to 1,000,000 items and 100 MB; lives on a single partition (throttling starts around 30k units/min) |
| MessagingService message size | 1 KB |
| MessagingService sends per server | `600 + 240 × players` per minute |
| MessagingService received per topic | `40 + 80 × servers` per minute |
| MessagingService subscriptions per server | `20 + 8 × players` |

## Core principles

### Data layout

1. **Few data stores, one key per player** (e.g. `Player_{UserId}`).
   - Static key patterns let Roblox's right-to-be-forgotten tooling find data.
   - With ProfileStore, call `profile:AddUserId()`.
2. **One table per player.** It updates atomically and makes rollbacks simple.

### Reading and writing

3. **Buffer in memory.** Use the DataStore only for:
   - the initial load;
   - periodic saves (the official sample uses 180 s; start each player's loop at a **random offset** so servers don't save in sync);
   - the save when the player leaves;
   - shutdown (`BindToClose`);
   - purchase checkpoints.
4. **`UpdateAsync` over `SetAsync`** whenever a write depends on the current value or several servers might write. `SetAsync` overwrites blindly.
5. **Retries must be ordered per key.** An older request that retries after a newer one succeeds overwrites newer data.
   - Use exponential backoff with jitter, capped.
   - Don't retry invalid-request errors.
   - Treat a failed write as **unknown outcome**: the backend may have committed it anyway.

### Session locking

6. **Session locking is mandatory** for any game with trading or currency. Without it, a quick rejoin or teleport lets server B load stale data before server A's final save, which causes duplication. The lock is:
   - written atomically in the same `UpdateAsync` that loads;
   - refreshed by autosaves;
   - released by the final save;
   - stealable only after it expires.

### Failure handling

7. **Never let fallback data overwrite real data.**
   - If the load fails, either kick the player with a clear message, or let them play on defaults with the profile **flagged as errored**.
   - Never save an errored profile, and block purchases for it.
8. **Version your schema.**
   - Store `SchemaVersion`.
   - Migrate step by step on load.
   - Never delete a shipped migration step.
   - Reconcile new keys from a template.

### Saving and maintenance

9. **Shutdown:** in `BindToClose`, save all players **in parallel**. Drop stale queued requests and keep only the latest per key. You have 30 s.
10. **Validate before persisting.** Saved data cannot contain:
   - Instances;
   - unbounded strings or keys;
   - invalid UTF-8;
   - NaN or inf.
11. **Hot shared keys** (global event state read by every server): shard the reads across copies such as `Weather_1..3`, and stagger polling with jitter.
12. **Don't use DataStore2** for new games; Roblox calls it legacy. Use ProfileStore, or follow the official player-data sample's design.

## Trades and other multi-player transactions

The leave-mid-trade duplication case: a player sends an item, leaves immediately, and their save fails, so they rejoin with the item while the other player also has it. To prevent it:
- Session-lock both players (both profiles are on this server).
- Validate both inventories **before** changing anything.
- Apply both changes in one step with no yields.
- Save both profiles immediately.
- If anything fails, revert both.
- For cross-server trades, use a MemoryStore-coordinated escrow. This is advanced; see [[Research Backlog]].

## Studio testing

- Enable **Studio Access to API Services** (Game Settings → Security) to test real DataStores. Better: use a **separate test universe** so you never touch live data.
- Studio has separate, static, possibly lower DataStore limits.
- ProfileStore has `.Mock` for tests that should never touch real DataStores.
- Test these cases:
  - first join;
  - rejoin;
  - fast server-hop (session lock);
  - Studio stop mid-save (shutdown);
  - a corrupted or old-schema profile (migration);
  - DataStore outage (errored profile, no overwrite).

## Operations

- **Data Stores Manager** (Creator Hub): inspect and edit keys, check size and cost.
- **Open Cloud** or the **Data Stores Batch Processor**: bulk fixes. Rate-limit them; they share the live budget.
- **Versions:** each key keeps versions, and only the latest counts toward storage. Use `ListVersionsAsync` and `GetVersionAsync` to restore a bad save.
- **Deleted data stores** stay restorable for **30 days**.

## Common mistakes

- One DataStore per player, or one key per stat.
- `SetAsync` from several servers on the same key.
- A naive retry loop that reorders writes.
- Saving default data after a failed load and wiping a real profile.
- Yielding in `PlayerRemoving` before the save (`WaitForChild`, remote calls).
- No `BindToClose`, or saving players one after another in it.
- Autosave loops on every server at the same moment.
- Renaming the store or key pattern, which silently wipes everyone.
- Granting Developer Products without recording `PurchaseId` in the save; see [[Developer Product Receipt Pipeline]].

## Production checklist

See [[Production Data Persistence Checklist]].

## Related notes

- [[Recipe - Player Data with ProfileStore]]
- [[Developer Product Receipt Pipeline]]
- [[Production Data Persistence Checklist]]
- [[Decision - DataStore vs MemoryStore]]
- [[Remote Security and Anti-Exploit]]

## Sources

AUTHORITATIVE, from creator-docs, 2026-10-13:
- [Best practices for data stores](https://create.roblox.com/docs/cloud-services/data-stores/best-practices)
- [Data store error codes and limits](https://create.roblox.com/docs/cloud-services/data-stores/error-codes-and-limits)
- [Implement player data and purchasing systems](https://create.roblox.com/docs/cloud-services/data-stores/player-data-purchasing)
- [Choose between cloud services](https://create.roblox.com/docs/cloud-services/data-stores-vs-memory-stores)
- [Memory stores](https://create.roblox.com/docs/cloud-services/memory-stores)
- [Memory store best practices](https://create.roblox.com/docs/cloud-services/memory-stores/best-practices)
- [MessagingService API (limits)](https://create.roblox.com/docs/reference/engine/classes/MessagingService)
- [DataModel:BindToClose](https://create.roblox.com/docs/reference/engine/classes/DataModel#BindToClose)

HIGH CONFIDENCE:
- [ProfileStore](https://github.com/MadStudioRoblox/ProfileStore) by loleris (MAD STUDIO), Apache-2.0. Repo state 2025-07-31.

## Last Verified

2026-10-14
