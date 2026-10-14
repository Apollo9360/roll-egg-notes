---
title: Decision - Part vs MeshPart vs Terrain
category: Decision Guides
tags: [decision, modeling]
status: active
source_quality: AUTHORITATIVE facts + labelled judgement
last_verified: 2026-10-14
aliases: [Part vs MeshPart vs Terrain, Studio modeling vs Blender]
---

# Decision - Part vs MeshPart vs Terrain

- **Parts:** blockouts, gameplay geometry (zones, platforms, triggers), simple architecture, anything designers tweak. Add **solid modeling** (Union, Negate) for simple compound shapes.
- **MeshParts (Blender):** organic or detailed shapes, props reused many times (instanced, so one draw call), PBR assets, characters, rigs, skinned meshes.
- **Terrain:** large natural landforms, water, caves, broad ground.

**Performance tie-breakers:**
- identical meshes instance well;
- unique unions are each separate assets;
- built-in materials are cheaper in memory than custom textures;
- collision fidelity matters more than visual triangles for physics cost.

Full table: [[Studio Modeling vs Blender]].

## Sources

[Solid modeling](https://create.roblox.com/docs/parts/solid-modeling), [Meshes](https://create.roblox.com/docs/parts/meshes), [Terrain](https://create.roblox.com/docs/parts/terrain). AUTHORITATIVE.

## Last Verified

2026-10-14
