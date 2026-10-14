---
title: Production Data Persistence Checklist
category: Checklists
tags: [checklist, data]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Persistence checklist]
---

# Production Data Persistence Checklist

**Design**
- [ ] Storage choice is justified (DataStore vs MemoryStore vs Configs) ([[Decision - DataStore vs MemoryStore]]).
- [ ] Few data stores; one key (or a few deterministic keys) per player; static key patterns.
- [ ] Data that must change atomically lives in one key.
- [ ] The schema is documented, with `SchemaVersion` and migrations that are never deleted once shipped.

**Runtime**
- [ ] Session locking; a lost session kicks the player with a message.
- [ ] Load: default data **only** with an errored flag; errored profiles never save; purchases disabled.
- [ ] Writes use UpdateAsync; retries are ordered per key with exponential backoff and jitter; invalid-request errors aren't retried.
- [ ] Failed writes are treated as unknown outcomes.
- [ ] Autosave staggered (random offset), shorter than the lock expiry.
- [ ] PlayerRemoving saves first, with no yields before the save.
- [ ] BindToClose saves all players in parallel, dropping stale queued requests; finishes within 30 s.
- [ ] Hot shared keys sharded for reads; polling jittered.

**Content**
- [ ] Values validated before saving: no Instances, size caps, valid UTF-8, finite numbers, bounded client-chosen keys.
- [ ] Developer Product PurchaseIds stored and confirmed saved before PurchaseGranted.
- [ ] Trades: validate both sides, commit both or neither, save immediately.

**Ops**
- [ ] A separate test universe for persistence testing.
- [ ] A rollback plan (version restore via `ListVersionsAsync`/`GetVersionAsync`, or Data Stores Manager).
- [ ] RTBF handled (AddUserId, or a custom webhook workflow).
- [ ] Request budgets monitored (`GetRequestBudgetForRequestType`, observability dashboard).

Details: [[Data Persistence Guide]] · [[Recipe - Player Data with ProfileStore]]

## Sources

[Data stores best practices](https://create.roblox.com/docs/cloud-services/data-stores/best-practices), [Player data and purchasing](https://create.roblox.com/docs/cloud-services/data-stores/player-data-purchasing), [Error codes and limits](https://create.roblox.com/docs/cloud-services/data-stores/error-codes-and-limits). AUTHORITATIVE.

## Last Verified

2026-10-14
