---
title: Blender Modeling Context
category: Context Packs
tags: [context-pack, blender, modeling, meshes, export]
status: active
source_quality: Distilled from the linked vault notes (sources cited there)
last_verified: 2026-10-14
aliases: [Modeling context, Blender context]
---

# Blender Modeling Context

## Summary

Load this before making or importing a mesh.

## Load these notes, in order

1. [[Studio Modeling vs Blender]] and [[Decision - Part vs MeshPart vs Terrain]].
2. [[Blender to Roblox Modeling Pipeline]].
3. [[Performance and Profiling Guide]] (rendering and memory).

## Non-negotiables (Roblox mesh specifications)

- At most **20,000 triangles** per mesh.
- Watertight; quads where possible, no n-gons.
- One UV set inside 0–1; one material per mesh.
- Apply transforms (scale 1, rotation 0) before export.

## Export and import settings

| Where | Setting |
|---|---|
| Blender FBX export | Apply Scalings = FBX Unit Scale; Add Leaf Bones off; Bake Animation off unless animated |
| Studio Importer | World Forward = Front; World Up = Top; Scale Unit = Stud |
| Normal maps | OpenGL convention (Blender's default) |

The docs disagree on the maximum texture resolution (4096 vs 1024); see the warning in [[Blender to Roblox Modeling Pipeline]] and [[Research Backlog]].

## Checklists to run

[[Blender Export Checklist]] · [[Performance Checklist]]

## Pipeline

Concept → Blockout → Model → UV → Texture/PBR → Optimize → Export (FBX) → Import → Collision and render settings → Test in game.

## Related notes

- [[START HERE]]
- [[AI Roblox Development Playbook]]
- [[Roblox Development Golden Rules]]

## Sources

The notes linked above. Each cites its official sources.

## Last Verified

2026-10-14. Facts here are copied from the linked notes, which cite their sources. If a number here and a note disagree, the note wins; fix this pack.
