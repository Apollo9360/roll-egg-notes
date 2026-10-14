---
title: Decision - When to Use StreamingEnabled
category: Decision Guides
tags: [decision, performance, streaming]
status: active
source_quality: AUTHORITATIVE facts + labelled judgement
last_verified: 2026-10-14
aliases: [StreamingEnabled decision, Should I use streaming]
---

# Decision - When to Use StreamingEnabled

**Use it (the default for new places):**
- for any world bigger than a small arena;
- for mobile-heavy audiences;
- when joins are slow or memory is high.

**Required for the Server Authority model.**

**Accept the costs.** Client code must tolerate parts streaming in and out:
- use CollectionService signals;
- handle missing objects;
- use Atomic models for things scripts need whole.

**Consider leaving it off only for** tiny single-room games, or where every client genuinely needs the whole map and the map is small. Measure memory on a phone before deciding.

**Tune:**
- the min and target radius;
- Persistent models sparingly;
- extra replication foci sparingly (each costs about as much as another player);
- SLIM for distance.

See [[StreamingEnabled and Large Worlds]].

## Sources

[Instance streaming](https://create.roblox.com/docs/workspace/streaming), [Improve performance: asset memory](https://create.roblox.com/docs/performance-optimization/improve). AUTHORITATIVE.

## Last Verified

2026-10-14
