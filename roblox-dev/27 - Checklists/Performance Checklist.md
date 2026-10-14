---
title: Performance Checklist
category: Checklists
tags: [checklist, performance]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Optimization checklist]
---

# Performance Checklist

**Measure**
- [ ] Baseline low-end device chosen; MicroProfiler, Developer Console and Script Profiler checked there.
- [ ] Server Heartbeat about 60; server memory below 50% of `6.25 GiB + 100 MiB × peak players`.
- [ ] 30+ minute soak: LuaHeap and InstanceCount flat over join/leave and respawn cycles.

**Scripts**
- [ ] No expensive per-frame work; event-driven; big jobs split across frames or moved to Parallel Luau.
- [ ] Connections and per-player tables cleaned up; `PlayerCharacterDestroyBehavior` or deferred Destroy on leave.

**Physics**
- [ ] Static parts anchored; Box or Hull collision on props; CanCollide, CanTouch and CanQuery off for decoration.
- [ ] Unused Humanoid states disabled; static NPCs use AnimationController; NPCs pooled and spawned near players.

**Rendering**
- [ ] Meshes uploaded once and instanced; trim sheets; built-in materials where possible.
- [ ] Shadows off on small and moving parts and lights; partial transparency minimized.
- [ ] Streaming plus SLIM for large worlds; RenderFidelity Automatic or Performance.

**Network**
- [ ] No server tweens; VFX on clients; deltas not full state; continuous data unreliable and throttled.

**Assets**
- [ ] Texture sizes right for object size; duplicate uploads removed; preload only critical assets.

Details: [[Performance and Profiling Guide]] · [[Roblox Stability Engineering]]

## Sources

[Improve performance](https://create.roblox.com/docs/performance-optimization/improve), [Identify issues](https://create.roblox.com/docs/performance-optimization/identify). AUTHORITATIVE.

## Last Verified

2026-10-14
