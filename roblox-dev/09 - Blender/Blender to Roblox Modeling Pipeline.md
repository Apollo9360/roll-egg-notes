---
title: Blender to Roblox Modeling Pipeline
category: Blender
tags: [blender, modeling, export, fbx, uv, pbr, surfaceappearance, collision, meshpart, low-poly]
status: active
source_quality: AUTHORITATIVE (Roblox art docs) + labelled common practice
last_verified: 2026-10-14
aliases: [Blender export settings, FBX export Roblox, Mesh import, SurfaceAppearance workflow, Blender Export Checklist source, Roblox-ready models]
---

# Blender to Roblox Modeling Pipeline

## Summary

`Concept → Blockout → Model → UV → Texture/PBR → Optimize → Export (FBX) → Import (Importer) → Collision and render settings → Test in game`

The hard rules come from Roblox's specifications:
- each mesh is at most **20,000 triangles**;
- watertight;
- quads where possible, no n-gons;
- one UV set inside 0–1;
- one material per mesh;
- applied transforms (scale 1, rotation 0).

Export FBX with **Apply Scalings = FBX Unit Scale**, **Add Leaf Bones off**, and **Bake Animation off** unless animated. Set the Studio Importer to World Forward = Front, World Up = Top, Scale Unit = Stud.

## Blender setup

Official guidance:
- Scene Properties → Units: **Unit System = None**, **Rotation = Degrees**. One Blender unit is then one stud.
- Blender is Z-up; Studio is Y-up. Handle this in the export and import settings below; never by rotating the model by eye.

## Modeling rules

| Rule | Why |
|---|---|
| ≤ 20,000 triangles per mesh (avatar items have their own budgets) | Hard import limit |
| Watertight: closed, no holes, no zero-thickness faces | Required by spec, and by solid modeling operations on meshes |
| Quads where possible, no n-gons | Clean triangulation and shading |
| Normals face outward | Otherwise the mesh looks inside-out |
| Apply transforms (Ctrl+A → All Transforms) before export; origin where the pivot should be | The pivot and scale import correctly |
| Unique names for meshes and bones; a mesh never shares a bone's name | The Importer needs them |
| Rigs: bones frozen at scale 1 / rotation 0, root at 0,0,0, ≤ 4 influences per vertex, none on root | Skinning spec |

### Common practice

These are community and professional conventions, not Roblox rules:
- **Silhouette first.** Roblox cameras are usually far away and phones are small. Exaggerate shapes; detail you can't see at 30 studs is wasted.
- **Low and mid poly.** Bevel only the edges that catch light. Use smooth-by-angle shading (Blender 4.1+ replaced Auto Smooth with the "Smooth by Angle" modifier and operator), and weighted normals for hard-surface props.
- **Modular kits.** Build walls, floors, trims and props on a grid (e.g. 4- or 8-stud modules) so they snap together in Studio. Upload each piece **once**.
- **Mirror and Array modifiers** while modeling. Apply them before export, or let the FBX exporter apply them ("Apply Modifiers" is on by default).
- **Decimate** high-poly sculpts or scans to budget, then bake detail into normal maps.

## UVs and textures

**UV and size rules** (official):
- **One UV set**, coordinates inside **0–1**. Overlapping UVs are allowed, which saves texture space on mirrored parts.
- **One material per mesh**. Split a mesh into parts if it really needs two materials.
- **Size by object scale.** Roughly 256² for a 5×5-stud object, 512² for 10×10 and 1024² for 20×20. PBR guidance: 256² per 2×2×2 units of asset.

> [!warning] Documented contradiction
> The texture specification says Roblox supports textures up to **4096×4096**. The UV-mapping section (written for avatar and PBR assets) says **1024×1024** is the maximum. Use 1024 or less unless you have tested larger maps for your use case. Memory scales with pixel count, so 1024² costs four times 512².

**PBR with `SurfaceAppearance`** (all official):
- **Maps:**
  - ColorMap (albedo, RGB, with no baked lighting);
  - NormalMap (**OpenGL, tangent space**; Blender renders and bakes in the OpenGL convention by default; maps from tools set to DirectX need the green channel inverted);
  - RoughnessMap and MetalnessMap (8-bit grayscale);
  - EmissiveMaskContent with EmissiveStrength and EmissiveTint.
- **AlphaMode:**
  - Overlay (the default; reveals `MeshPart.Color` where the map is transparent);
  - Opaque;
  - Transparency (cut-outs: lace, leaves);
  - TintMask.
- **Not scriptable at runtime** for map changes; it needs preprocessing. To tint variants, use `SurfaceAppearance.Color` on one shared texture instead of uploading copies.

**Choosing texture tech:**
- **MaterialVariant** in MaterialService applies a custom PBR material plus physical properties to parts and meshes. Use it for world surfaces such as studs, stone and wood.
- **SurfaceAppearance** wins over a MaterialVariant's maps when both are present.
- **Built-in materials** use far less memory than custom textures. Use them for most of the world, and save custom textures for hero assets.
- **Trim sheets** cover environments (one texture, many meshes).

## Export (Blender → FBX)

Official steps (`art/modeling/export-requirements`, `art/blender`):
1. Select the objects, then File → Export → **FBX**. Enable **Limit to Selected Objects**.
2. **Path Mode: Copy**, with **Embed Textures** on, so textures travel with the FBX.
3. **Transform → Apply Scalings: FBX Unit Scale.** Leave other scale values at 1.0.
   - Alternatives if the scale comes out wrong: export Scale 0.01, or Scene Unit Scale 0.01. The second causes Blender camera and tool problems.
4. **Forward: Z Forward, Up: Y Up.**
5. **Armature:** turn **Add Leaf Bones** off.
6. **Bake Animation** off, unless you're exporting keyframes.

glTF (.glb/.gltf) and OBJ are also accepted by the Importer. FBX is required for rigs, skinning and cages.

The **Roblox Blender plugin** (official, open source, built on Open Cloud) uploads straight from Blender to your Studio session without the file round trip.

## Import (Studio Importer)

Official settings:
- **File General:**
  - **Import Only as a Model** to group several objects;
  - **Upload to Roblox** to make it a reusable asset ID;
  - **Creator** = you or the owning group;
  - **Insert Using Scene Position** if the Blender placement matters.
- **File Transform:** World Forward = **Front**, World Up = **Top**.
- **File Geometry:** Scale Unit = **Stud**.
- **Naming conventions the Importer converts automatically:**
  - `_Att` → Attachment;
  - `_OuterCage` → WrapTarget;
  - `_InnerCage` + `_OuterCage` → WrapLayer;
  - heads with facial data → FaceControls.
- **The Importer does not de-duplicate meshes.** Import each unique asset once, then duplicate it in Studio. Importing a whole map as one file creates duplicate assets, costing memory and draw calls ([[Performance and Profiling Guide]]).

## After import: physics and rendering settings

| Property | Choose |
|---|---|
| `CollisionFidelity` | **Box** for small or decorative pieces. **Hull** for convex-ish objects. **Default** for concave interactive pieces. **PreciseConvexDecomposition** only when precise collision matters. **Tunable** gives a precision slider. For large complex meshes, use invisible box parts as collision. |
| `CanCollide` / `CanTouch` / `CanQuery` | Turn all three off for pure decoration. Collision geometry still costs memory, so also set Box. |
| `RenderFidelity` | **Automatic** (the default): highest detail under 250 studs, medium at 250–500, lowest beyond 500. **Performance** for background props. **Precise** only for hero assets. |
| `DoubleSided` | Off unless the mesh is a single-sided shell such as cloth or leaves |
| `CastShadow` | Off for small and distant props |
| Anchored | Yes for anything static |

## When to use Studio instead

See [[Studio Modeling vs Blender]].

## Checklist

[[Blender Export Checklist]]

## Common mistakes

- Model imports 100× too big. Fix: Apply Scalings = FBX Unit Scale.
- Model lies on its back or faces the wrong way. Fix: Forward/Up export settings, and Front/Top in the Importer.
- Unapplied scale or rotation, which breaks pivots, rig binding and animation.
- DirectX normal maps, where the lighting looks inverted on bumps.
- A 4K texture on a pebble.
- Fifty uniquely uploaded copies of the same rock.
- Precise collision on every prop.
- Leaf bones exported. They add junk joints to rigs.

## Related notes

- [[Studio Modeling vs Blender]]
- [[R15 Blender Animation Pipeline]]
- [[Blender Export Checklist]]
- [[Performance and Profiling Guide]]
- [[Blender Modeling Context]]

## Sources

AUTHORITATIVE, from creator-docs 2026-10-13:
- [General specifications](https://create.roblox.com/docs/art/modeling/specifications)
- [Export settings](https://create.roblox.com/docs/art/modeling/export-requirements)
- [Blender guide](https://create.roblox.com/docs/art/blender)
- [Texture specifications](https://create.roblox.com/docs/art/modeling/texture-specifications)
- [PBR textures (SurfaceAppearance)](https://create.roblox.com/docs/art/modeling/surface-appearance)
- [Meshes](https://create.roblox.com/docs/parts/meshes)
- [Roblox Blender plugin](https://create.roblox.com/docs/art/modeling/roblox-blender-plugin)
- [Importer](https://create.roblox.com/docs/studio/importer)

Blender behaviour, verified 2026-10-14:
- [Blender 4.1 release notes: Modeling](https://developer.blender.org/docs/release_notes/4.1/modeling/): Auto Smooth was replaced by the Smooth by Angle modifier node group and the "Shade Smooth by Angle" / "Set Sharpness by Angle" operators. AUTHORITATIVE.
- [Blender manual: Normal Map node](https://docs.blender.org/manual/en/latest/render/shader_nodes/vector/normal_map.html): OpenGL convention by default for rendering and baking. AUTHORITATIVE.

## Last Verified

2026-10-14
