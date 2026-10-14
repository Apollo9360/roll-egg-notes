---
title: DataStore Checklist
category: Checklists
tags: [checklist, data]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Data checklist]
---

# DataStore Checklist

Quick version of [[Production Data Persistence Checklist]]:

- [ ] One store, one key per player (`Player_{UserId}`); `AddUserId` / RTBF compatible.
- [ ] Session locking (ProfileStore or equivalent).
- [ ] Load-failure path never saves defaults over real data; purchases blocked while errored.
- [ ] `UpdateAsync` for dependent writes; ordered per-key retries with backoff and jitter.
- [ ] Autosave with a random per-player offset; save on leave; parallel saves in `BindToClose` (30 s).
- [ ] `SchemaVersion` + migrate **before** Reconcile.
- [ ] Saved values: JSON-safe, valid UTF-8, finite numbers, bounded size (4 MB per key).
- [ ] Tested in a separate universe: first join, rejoin, fast server-hop, old schema, outage, shutdown mid-save.

## Sources

[Data store best practices](https://create.roblox.com/docs/cloud-services/data-stores/best-practices). AUTHORITATIVE.

## Last Verified

2026-10-14
