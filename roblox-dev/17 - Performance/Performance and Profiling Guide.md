---
title: Performance and Profiling Guide
category: Performance
tags: [performance, profiling, microprofiler, memory, rendering, physics, humanoids, network, mobile]
status: active
source_quality: AUTHORITATIVE
last_verified: 2026-10-14
aliases: [Optimization guide, MicroProfiler, Memory leaks, Lag, FPS drops, Draw calls, Low-end devices]
---

# Performance and Profiling Guide

## Summary

**Measure first, on the device you care about.** Pick one baseline low-end device and test on it throughout development.

Performance problems fall into five buckets:
- script compute;
- memory (Lua heap, instances, assets);
- physics;
- rendering (client only);
- network.

Each has a specific tool and a short list of fixes, below. The frame budget at 60 FPS is **16.67 ms**. Server heartbeat is capped at 60.

## Diagnose: which tool for which symptom

| Symptom | Tool | What to look at |
|---|---|---|
| Client FPS low or spiky | **MicroProfiler** (Ctrl/⌘+F6; Ctrl/⌘+P pauses into detailed mode); Shift+F5 stats summary; Shift+F2 render stats | Frames over 16.67 ms; which scope dominates |
| Server lag, high ping | Developer Console (F9) → **Server Jobs** → Heartbeat **Steps Per Sec** (below 60 is bad); **Server Stats** for average ping; server MicroProfiler | Heartbeat steps per second; data ping vs network ping |
| Which script is costly | **Script Profiler** (in the Developer Console) for client and server | CPU time per function. Native functions show `<native>`. |
| Your own code section | `debug.profilebegin("Label")` … `debug.profileend()` | Shows as a labeled scope in the MicroProfiler |
| Memory growing over time | Developer Console → **Memory**: `LuaHeap`, `InstanceCount`, `PlaceScriptMemory`; Studio **Luau heap** snapshots | Steady growth = leak |
| Client memory or crashes | Developer Console Memory on a **real device**: `GraphicsMeshParts`, `GraphicsTexture`, `Sounds`; Creator Dashboard Performance (crash rate) | Investigate if crash rate rises above 2–3% |
| Slow join | Stopwatch; `game.Loaded` timer in ReplicatedFirst; Studio Settings → Network → **Print Join Size Breakdown** | Largest instances by size |
| Network spikes | MicroProfiler network view; Shift+F3 network stats | Remote volume, replication of big trees |
| Streaming issues | Shift+F3, then Shift+1 cycles to the Streaming panel | Loaded regions |

Studio's own overhead skews numbers, because it runs the server and client together. Confirm on the real client and a real low-end device. The **device emulator** is good for layout but **not** for memory.

### Official reference numbers

- **Server memory** = `6.25 GiB + 100 MiB × peak players`. Keep usage below 50%. Memory doesn't shrink when players leave.
- **Frame budget**: 16.67 ms at 60 FPS. Windows players can raise the client cap to 240 FPS.
- **Example device budget**: the docs show, as an *example* of profiling a baseline phone, staying under about 1,000 draw calls and about 1,000,000 triangles. **Measure your own baseline device**; this is not a universal limit.

## Fixes by bucket

### 1. Script compute

- **Don't run expensive code on RunService events** (PreRender, PreAnimation, PreSimulation, PostSimulation, Heartbeat). Use them only where per-frame work is essential, such as camera and visual interpolation. Prefer events and timers; see [[Decision - Polling vs Events]].
- **Spread big jobs across frames.** Do some work, `task.wait()`, continue. Code that takes 100 ms once a second causes a visible hitch every second.
- **Cache results** instead of re-querying: `FindFirstChild` chains, `GetDescendants`, raycasts on static geometry.
- **Large table operations** (serialize, deep clone, recursion over big structures) are expensive. Do them rarely, and incrementally.
- **Parallel Luau** for independent heavy work such as per-NPC logic, hit validation and procedural generation:
  - put the scripts under `Actor`s;
  - use `task.desynchronize()` or `ConnectParallel`;
  - `require` only in serial context;
  - most DataModel **writes** must happen serially;
  - use several Actors split by logical unit;
  - communicate with `Actor:SendMessage` or `SharedTable`.
- **Native code** (`@native`) for hot numeric server code; see [[Luau Language and Performance Guide]].

### 2. Script memory (leaks)

- **Connections are never collected while connected.** Everything their callbacks reference stays alive.
- **`Player` objects and characters are NOT destroyed automatically when a player leaves**, so connections on them leak. Fix:
  - enable `Workspace.PlayerCharacterDestroyBehavior`; or
  - in `PlayerRemoving`, call `task.defer(player.Destroy, player)`, and in `CharacterRemoving`, `task.defer(character.Destroy, character)`.
- **Per-player tables**: clear them in `PlayerRemoving`.
- **Pattern**: [[Cleanup Pattern (Maid Janitor Trove)]].

### 3. Physics

- **Anchor** everything that doesn't need simulation.
- **Keep adaptive timestepping** (60/120/240 Hz chosen per mechanism). Fixed mode forces 240 Hz everywhere.
- **Fewer constraints**, and less self-collision (no-collision constraints on ragdoll limbs).
- **CollisionFidelity:**
  - `Box` for small or non-interactive meshes (safe for any small anchored part);
  - `Hull` for medium objects;
  - invisible box parts as custom collision for large complex meshes;
  - `PreciseConvexDecomposition` only where precision matters.
- **No collision needed?** Set `CanCollide`, `CanTouch` and `CanQuery` all to false, and use Box fidelity. Collision geometry is stored in memory even when unused.
- **Debugging:** viewport Visualization Options → **Collision fidelity**. In the Explorer, filter `CollisionFidelity=PreciseConvexDecomposition` to find costly meshes.

### 4. Humanoids and NPCs

- **Disable unused states** with `Humanoid:SetStateEnabled` (for example `Climbing`).
- **Static NPCs:** no Humanoid; use an `AnimationController`. Moving NPCs can use a custom mover plus an AnimationController.
- **Play NPC animations on the client.** Create the Animator client-side and animate only nearby NPCs.
- **Pool NPCs** instead of destroy and respawn. Spawn near players only, and cull them when players leave the area.
- **Avoid changing the avatar hierarchy after spawn.** Animate procedurally with `Motor6D.Transform`, not C0 and C1. Attach accessories outside the avatar model.
- **Watch for FastCluster rebuilds:** `updateInvalidatedFastClusters` over 4 ms in the MicroProfiler means avatar or skinned-mesh churn, size changes or layered clothing.

More in [[NPC and AI Engineering]].

### 5. Rendering (client)

- **Draw calls dominate.** Identical meshes (same MeshId, same SurfaceAppearance or TextureID, or same material) are **instanced** into one draw call.
  - Upload each mesh **once** and duplicate it in Studio. **Never import a whole map as one file**; the Importer doesn't de-duplicate.
  - Use Packages for reuse.
- **Decals, textures and particles batch poorly.** Changing ParticleEmitter properties at runtime can hurt badly.
- **LOD:**
  - streaming plus `Model.LevelOfDetail = SLIM` for distant models;
  - `Workspace.EnableSLIMAvatars` for crowds;
  - `MeshPart.RenderFidelity = Automatic` or `Performance`.
- **Shadows:**
  - `CastShadow = false` on small, distant and moving parts;
  - `Light.Shadows = false` where it isn't needed;
  - fewer lights with smaller range.
  - Shadows turn off entirely below graphics quality 4.
- **Transparency:** only 0 or 1 where possible. Avoid stacked partially transparent surfaces (overdraw).
- **Moving skinned meshes without a Humanoid** rebuild spatial clusters. Adding a Humanoid to that model forces a single cluster. Profile before and after.

### 6. Network

- Don't replicate per-frame data that hasn't changed; send only deltas and only what's needed (not the whole inventory).
- **Never tween on the server.** Every frame replicates, and it looks jittery. Tween on clients.
- Create VFX, view models and cosmetic instances **on clients**. The server only decides outcomes.
- Chunk large instance trees, such as maps, across frames.
- Remove Animation Editor leftovers (`AnimSaves`) from rigs before publishing.

See [[Roblox Networking Best Practices]].

### 7. Assets and memory

- **Enable StreamingEnabled** for any sizable world; see [[StreamingEnabled and Large Worlds]].
- **Texture memory depends on pixels, not file size.** 1024² uses four times the memory of 512². Most UI images need at most 512², minor ones at most 256².
- **Duplicate uploads waste memory.** Reuse IDs. Tint one texture with `SurfaceAppearance.Color` instead of uploading coloured copies.
- **Trim sheets** for environments; **sprite sheets** with `ImageRectOffset`/`ImageRectSize` for UI.
- **Built-in materials** use far less memory than custom textures.
- **Audio** adds up. Load it per area, not all at start.
- **Preload only** loading-screen images, critical menu images and spawn-area assets. Never `PreloadAsync(workspace)`. Offer a Skip button.
- **ReplicatedStorage** downloads everything to every client. Keep server-only assets in ServerStorage.

## Object pooling

Use it for things created and destroyed many times per second: projectiles, hit sparks, damage numbers, NPCs that respawn. See [[Recipe - Object Pool]].

Don't pool things that are rare, or that have a complex state that is hard to reset.

## Production checklist

[[Performance Checklist]]

## Related notes

- [[Roblox Stability Engineering]]
- [[StreamingEnabled and Large Worlds]]
- [[Luau Language and Performance Guide]]
- [[Optimization Context]]

## Sources

All AUTHORITATIVE, from creator-docs, 2026-10-13:
- [Improve performance](https://create.roblox.com/docs/performance-optimization/improve)
- [Identify performance issues](https://create.roblox.com/docs/performance-optimization/identify)
- [Design for performance](https://create.roblox.com/docs/performance-optimization/design)
- [MicroProfiler](https://create.roblox.com/docs/performance-optimization/microprofiler)
- [Script Profiler](https://create.roblox.com/docs/studio/optimization/scriptprofiler)
- [Memory usage](https://create.roblox.com/docs/studio/optimization/memory-usage)
- [Parallel Luau](https://create.roblox.com/docs/scripting/multithreading)

## Last Verified

2026-10-14
