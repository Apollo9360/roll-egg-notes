---
title: Decision - DataStore vs MemoryStore
category: Decision Guides
tags: [decision, data]
status: active
source_quality: AUTHORITATIVE facts + labelled judgement
last_verified: 2026-10-14
aliases: [DataStore vs MemoryStore, Which storage]
---

# Decision - DataStore vs MemoryStore

| Need | Choose |
|---|---|
| Permanent player progress, inventory, purchases | **DataStore** (standard), session-locked |
| Ranked numbers (all-time leaderboard) | **OrderedDataStore** |
| Fast-changing shared state that expires (matchmaking queue, live leaderboard, server list, auction house listings) | **MemoryStore**: queue, sorted map or hash map; expires after at most 45 days |
| Values tuned live, feature flags | **Configs** (read-only in game; about 5-minute rollout) |
| API keys and tokens | **Secrets store** |
| Fire-and-forget cross-server broadcast | **MessagingService** (best effort, 1 KB) |
| Per-server temporary state | Luau tables |

**Rule of thumb:** if losing it is unacceptable, use a DataStore. If it's fast and temporary, use a MemoryStore. If it's tuned by designers, use Configs.

See [[Data Persistence Guide]].

## Sources

[Choose between cloud services](https://create.roblox.com/docs/cloud-services/data-stores-vs-memory-stores). AUTHORITATIVE.

## Last Verified

2026-10-14
