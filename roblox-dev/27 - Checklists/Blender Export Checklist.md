---
title: Blender Export Checklist
category: Checklists
tags: [checklist, blender, modeling]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Export checklist, FBX checklist]
---

# Blender Export Checklist

**Before export**
- [ ] Scene units: Unit System None, Rotation Degrees.
- [ ] ≤ 20,000 triangles per mesh; watertight; no n-gons; no zero-thickness faces; normals outward.
- [ ] Transforms applied (scale 1, rotation 0); origin at the intended pivot.
- [ ] One UV set inside 0–1; one material per mesh; texture sizes match object size.
- [ ] Normal maps in OpenGL format; roughness and metal maps grayscale.
- [ ] Unique names; meshes don't share names with bones; rigs frozen, root at 0,0,0, ≤ 4 influences per vertex.

**Export (FBX)**
- [ ] Limit to Selected Objects; Path Mode Copy with Embed Textures on.
- [ ] Apply Scalings: **FBX Unit Scale**; Forward **Z**, Up **Y**.
- [ ] Add Leaf Bones **off**; Bake Animation off (on only for animation exports, one clip per file).

**Import (Studio)**
- [ ] World Forward Front, World Up Top, Scale Unit Stud.
- [ ] Each unique mesh imported once, then duplicated in Studio.
- [ ] CollisionFidelity chosen (Box or Hull for props); decorative pieces have CanCollide, CanTouch and CanQuery off.
- [ ] RenderFidelity Automatic (Performance for background); CastShadow off for small props.
- [ ] SurfaceAppearance or MaterialVariant set up; checked on a phone at low graphics.

Details: [[Blender to Roblox Modeling Pipeline]]

## Sources

[Specifications](https://create.roblox.com/docs/art/modeling/specifications), [Export settings](https://create.roblox.com/docs/art/modeling/export-requirements), [Blender guide](https://create.roblox.com/docs/art/blender). AUTHORITATIVE.

## Last Verified

2026-10-14
