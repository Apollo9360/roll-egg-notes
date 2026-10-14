---
tags: [huds-guide, technical]
---

# Models, meshes, textures and building - Poly budgets

Up: [[Models, meshes, textures and building]] · Previous: [[Models, meshes, textures and building - Blender to Studio]] · Next: [[Models, meshes, textures and building - Unions and collisions]]

| Asset | Budget | Confidence |
|---|---|---|
| Simple props | Under ~1.5k faces | Medium |
| Any prop | Over 5k faces is bad | Medium |
| AI-generated models | Target ~2–4k tris | Medium |
| Crate example | 156 tris modelled; 108 after inset faces (Ctrl+I); or a textured 6-sided cube | Medium |

- Raw AI meshes come out heavy: Meshy gives 100k+ polys with poor topology, and simple objects have hit 200k tris.
- Tripo gives 4–10k tris with weaker textures; a 3D modeler rated it above Meshy.
- Decimate and Merge by Distance reduce AI meshes, but wreck textures on messy topology.

---
Related concepts: [[AI Tools]]
