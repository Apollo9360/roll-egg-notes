---
title: Decision - Humanoid vs Custom NPC Controller
category: Decision Guides
tags: [decision, npc, performance]
status: active
source_quality: AUTHORITATIVE facts + labelled judgement
last_verified: 2026-10-14
aliases: [Humanoid vs custom NPC, AnimationController NPC]
---

# Decision - Humanoid vs Custom NPC Controller

| NPC | Choose |
|---|---|
| Player-like NPC that walks, jumps, climbs and uses tools, a few at a time | **Humanoid** with unused states disabled |
| Static NPC (shopkeeper, decoration) | **AnimationController + Animator**, no Humanoid |
| Many moving enemies or critters | A custom mover (AlignPosition, LinearVelocity, or CFrame on anchored models) plus an **AnimationController**; animate on clients near players |
| Physics-heavy or competitive | Consider server authority; NPCs owned by the server (`SetNetworkOwner(nil)`) |

Humanoids carry a significant CPU cost; the official docs recommend alternatives for NPCs that don't need them. See [[NPC and AI Engineering]].

## Sources

[Improve performance: Humanoids](https://create.roblox.com/docs/performance-optimization/improve). AUTHORITATIVE.

## Last Verified

2026-10-14
