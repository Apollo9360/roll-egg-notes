---
title: Studio Modeling vs Blender
category: 3D Modeling
tags: [modeling, studio, solid-modeling, csg, unions, meshes, terrain, parts, decision]
status: active
source_quality: AUTHORITATIVE (Roblox docs) + labelled judgement
last_verified: 2026-10-14
aliases: [Part vs MeshPart vs Terrain, Solid modeling, Unions, CSG, Studio building]
---

# Studio Modeling vs Blender

## Summary

- **Studio parts and solid modeling** suit gameplay geometry, blockouts, modular architecture and anything designers tweak often.
- **Blender meshes** suit organic and detailed shapes, props reused many times, anything that needs UVs and PBR, rigs, and skinned or animated geometry.
- **Terrain** suits large natural landforms and water.

Most good Roblox worlds use all three.

## Decision table

| Need | Use | Why |
|---|---|---|
| Blockout or graybox a level | **Parts** | Fastest iteration; snapping and pivots; anyone can edit |
| Gameplay geometry (zones, triggers, platforms, walls) | **Parts**, anchored, often invisible | Exact sizes, easy to script, cheap collisions |
| Architecture from simple shapes | Parts plus **solid modeling** (Union, Intersect, Negate) | No external tools; CSG works on watertight meshes too |
| Organic shapes: rocks, trees, creatures | **Blender** MeshPart | Topology control, sculpting, UVs |
| A prop repeated many times | **Blender**, uploaded once and duplicated | Instancing means one draw call for identical meshes |
| PBR-textured hero asset | **Blender** + SurfaceAppearance | UVs needed |
| Characters, rigs, skinned or animated meshes | **Blender** | Bones, weights, cages |
| Mountains, hills, caves, water, large ground | **Terrain** (Terrain Editor, generation) | Built-in materials, water, cheap at scale |
| Runtime slicing or shattering | `GeometryService` (`SweepPartAsync`, `FragmentAsync`) on watertight geometry | Engine solid-modeling API |

## Studio modeling essentials

- **Solid modeling tools** (Model tab):
  - **Union** Shift+Ctrl/⌘+G;
  - **Intersect** Shift+Ctrl/⌘+I;
  - **Negate** Shift+Ctrl/⌘+N;
  - **Separate** Shift+Ctrl/⌘+U.
- **They need watertight geometry.** Toggle `DoubleSided` on a mesh to spot shells: if anything changes, it's a shell that solid modeling can't fix.
- **Pivots and snapping** (Pivot tools, grid snap, rotation increments) keep modular kits aligned. Set a model's `PrimaryPart` or pivot so code can position it with `PivotTo`.
- **Organization:**
  - Models for geometric groups and Folders for logic groups;
  - **tags** (CollectionService) for behaviour;
  - **attributes** for configuration;
  - **Packages** for reusable kits, so one edit updates every copy.
- **Materials:** built-in materials, plus **MaterialVariants** for a custom look across parts. Far cheaper in memory than unique textures.
- **Collision:** unions and meshes use CollisionFidelity; set Box or Hull where precision isn't needed ([[Blender to Roblox Modeling Pipeline]]).
- **Large worlds:** StreamingEnabled with sensible model streaming modes ([[StreamingEnabled and Large Worlds]]).

## Union vs mesh

These are trade-offs, not hard rules:
- A union is editable later (Separate) and quick to make. Very complex unions get heavy collision and rendering geometry, and every unique union is its own asset.
- A Blender mesh gives clean topology, UVs and controlled triangle counts.
- For a shape used once, a union is fine. For a shape used many times, or a detailed one, model it in Blender.

## Related notes

- [[Blender to Roblox Modeling Pipeline]]
- [[Performance and Profiling Guide]]
- [[Lighting and Environment Guide]]
- [[Decision - Part vs MeshPart vs Terrain]]

## Sources

- [Solid modeling](https://create.roblox.com/docs/parts/solid-modeling), [Meshes](https://create.roblox.com/docs/parts/meshes), [Parts](https://create.roblox.com/docs/parts), [Terrain](https://create.roblox.com/docs/parts/terrain), [Design for performance (packages, reuse)](https://create.roblox.com/docs/performance-optimization/design). AUTHORITATIVE.

## Last Verified

2026-10-14
