---
title: Roblox Stability Engineering
category: Performance
tags: [stability, memory-leaks, race-conditions, respawn, shutdown, teleport, reliability]
status: active
source_quality: Synthesized from AUTHORITATIVE docs; each item cites its source note
last_verified: 2026-10-14
aliases: [Stability, Reliability, Crash prevention, Race conditions, Failure modes]
---

# Roblox Stability Engineering

## Summary

The bugs that take down a live Roblox game are rarely clever; they are lifecycle bugs. Most fall into four groups:
- things not cleaned up (leaks);
- things done twice (duplicate initialization, double grants);
- things done in the wrong order (races, nil state);
- things assumed to succeed (DataStores, teleports, assets).

Below, each failure mode is paired with its cause and its fix.

## Failure-mode catalogue

| Failure | Typical cause | Prevention |
|---|---|---|
| **Memory leak (server)** | Connections on `Player` or character objects that are never destroyed; per-player tables never cleared | `Workspace.PlayerCharacterDestroyBehavior`, or defer `Destroy` in PlayerRemoving and CharacterRemoving; clear tables on PlayerRemoving; [[Cleanup Pattern (Maid Janitor Trove)]] |
| **Connection leak** | Connecting inside CharacterAdded or a UI open without ever disconnecting | One cleanup object per lifetime (life, round, screen) |
| **Race condition / item duplication** | Yield between a check and a write; two servers writing the same key | No yields between check and write; session locking; `UpdateAsync` ([[Data Persistence Guide]]) |
| **Duplicate initialization** | A module doing work at require time; a LocalScript in `StarterCharacterScripts` re-running on every respawn; `Starter*` containers running original plus copy | Single bootstrap with `start()` ([[Roblox Project Architecture]]); idempotent `start` guarded by a flag |
| **Save corruption** | Saving defaults after a failed load; non-JSON values; schema changes without migration | Errored-profile flag; validate before save; `SchemaVersion` with migrations |
| **Lost purchases** | Granting before the save is confirmed; no `PurchaseId` record | [[Developer Product Receipt Pipeline]] |
| **Remote abuse** | No validation or rate limits | [[Recipe - Secure Remote Handler]] |
| **Client/server desync** | Client predicts and the server rejects without telling the client; the client caches state that the server later changes | Server sends the authoritative result; the client reconciles. Replicate state with attributes or replication, not one-shot messages alone. |
| **Runaway loops** | `while true` without a yield; a loop that never checks its owner still exists | Always yield; `while obj.Parent do`; register the thread with a cleanup object |
| **Excessive task spawning** | `task.spawn` per event per frame; unbounded `task.delay` chains | Batch work per tick; one loop per system, not per object |
| **Giant scripts** | Everything in one Script | Split by domain into services and controllers |
| **Cyclic requires** | A requires B, B requires A | Extract a shared module, or inject dependencies in `init` |
| **Nil-state bugs** | Assuming a character, data or streamed part exists | Type narrowing (`if not x then return end`); `waitFor` helpers with timeouts; tolerate streaming |
| **Stale references** | Holding the old character after respawn; holding a streamed-out part | Re-resolve on CharacterAdded; CollectionService removed signals |
| **Respawn bugs** | State stored on the character, which resets; UI with `ResetOnSpawn = true` losing controller references | Keep state on the server or Player, not the character; set `ResetOnSpawn = false` on persistent UI |
| **PlayerRemoving bugs** | Yielding before save; reading a character that is already gone | Save first, from in-memory data; never `WaitForChild` in PlayerRemoving |
| **Teleport failures** | Teleports can fail (full server, network, unauthorized) | Wrap `TeleportAsync` in `pcall`; retry with backoff; listen to `TeleportInitFailed`; show a fallback UI ([[Teleportation and Cross-Server]]) |
| **Shutdown-save failures** | No `BindToClose`; saving players one after another | Parallel saves in `BindToClose`, within 30 s (ProfileStore does this) |
| **Excessive replication** | Server tweens; per-frame property writes; huge instance trees created at runtime | Tween on clients; throttle writes; chunk map loads |
| **Excessive instance creation** | Clone/Destroy churn for effects | [[Recipe - Object Pool]]; effects on clients |
| **Frame-rate-dependent logic** | Adding fixed amounts per Heartbeat without `dt` | Multiply by `deltaTime`; use timestamps (`os.clock()` / `time()`) for cooldowns |
| **Mobile crashes (out of memory)** | Large textures; everything in ReplicatedStorage; no streaming | Streaming, texture-size discipline, test on a baseline phone ([[Performance and Profiling Guide]]) |
| **Network spikes** | Bursts of reliable remotes; big payloads | Batch, delta-encode, use unreliable remotes for continuous data ([[Roblox Networking Best Practices]]) |
| **Deferred-event surprises** | Code relying on Immediate signal order | Write for `SignalBehavior = Deferred` ([[Luau Language and Performance Guide]]) |

## Defensive habits

1. **Every `start()` is idempotent.** Guard it with `if started then return end`.
2. **Every yield is a possible interleaving.** Re-check state after `task.wait`, `WaitForChild` or any `*Async` call.
3. **Every `*Async` call can fail.** Wrap it in `pcall`, decide on retry versus fallback, and tell the player when it matters.
4. **Every per-player resource has a PlayerRemoving path.**
5. **Every loop has an exit condition** tied to its owner's lifetime.
6. **Time comes from one source.** Use `os.clock()` for durations on one machine, `workspace:GetServerTimeNow()` for times shared between client and server, and `time()` inside server-authority simulation.
7. **Log the unexpected** with IDs. Silence hides incidents.

## Stability test pass before release

See [[Release Checklist]] and [[Testing and QA Guide]]. At minimum:
- join, leave and rejoin quickly;
- respawn 20 times;
- run 8 clients in Server & Clients;
- Network Simulator on a bad mobile preset;
- leave the server running for 30 minutes or more while watching LuaHeap and InstanceCount;
- force a shutdown during a save.

## Related notes

- [[Performance and Profiling Guide]]
- [[Data Persistence Guide]]
- [[Cleanup Pattern (Maid Janitor Trove)]]
- [[Bug Investigation Checklist]]

## Sources

AUTHORITATIVE:
- [Improve performance (leaks, Player destroy behaviour)](https://create.roblox.com/docs/performance-optimization/improve)
- [Deferred events](https://create.roblox.com/docs/scripting/events/deferred)
- [Player data and purchasing](https://create.roblox.com/docs/cloud-services/data-stores/player-data-purchasing)
- [BindToClose](https://create.roblox.com/docs/reference/engine/classes/DataModel#BindToClose)

## Last Verified

2026-10-14
