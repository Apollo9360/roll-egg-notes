---
title: Decision - When to Use Object Pooling
category: Decision Guides
tags: [decision, performance]
status: active
source_quality: AUTHORITATIVE facts + labelled judgement
last_verified: 2026-10-14
aliases: [Object pooling decision]
---

# Decision - When to Use Object Pooling

**Pool** when an Instance is created and destroyed **frequently** (many per second, or expensive NPC respawns):
- projectiles;
- hit sparks;
- damage numbers;
- enemies in waves.

**Don't pool** when:
- objects are rare;
- they have complex hidden state that is hard to reset;
- or memory matters more than CPU (pools hold memory).

**Better first:** create cosmetic effects on clients instead of the server, and avoid creating instances at all where a property change would do.

Recipe: [[Recipe - Object Pool]].

## Sources

[Improve performance: pool NPC models](https://create.roblox.com/docs/performance-optimization/improve). AUTHORITATIVE.

## Last Verified

2026-10-14
