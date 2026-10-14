---
tags: [huds-guide, technical]
---

# Models, meshes, textures and building - Blender to Studio

Up: [[Models, meshes, textures and building]] · Next: [[Models, meshes, textures and building - Poly budgets]]

- Flipped normals: enable Face Orientation, select red faces, Shift+N to recalculate. Or enable MeshPart.DoubleSided.
- Negative scale issues: apply scale (Ctrl+A, then Scale). Export FBX.
- Blocky-looking meshes: set RenderFidelity to Precise.
- Physically based rendering (PBR) textures turning transparent: bake textures first, then export FBX. Or upload the texture as a decal and set TextureID.
- Modeling flow: start from a cube or sphere, edit, Decimate for low poly, UV unwrap, texture paint.
- Bake normal maps from high-poly onto low-poly targets ([tutorial](https://www.youtube.com/watch?v=Zxl38NpPWdo)).
- Custom rigs: export from Studio as OBJ, add bones in Blender, re-import, then animate in Moon Animator.
- Blender suits complex shapes; Studio parts are faster for stud-style builds. One beginner took 3 h in Studio versus 12 h in Blender for the same build.
- Rebuild simple assets natively in Studio, and import greyscale meshes tinted with Studio gradients, for performance.

---
Related concepts: [[Onboarding and Tutorials]]
