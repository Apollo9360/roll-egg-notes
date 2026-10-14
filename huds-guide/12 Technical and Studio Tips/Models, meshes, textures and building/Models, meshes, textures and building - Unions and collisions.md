---
tags: [huds-guide, technical]
---

# Models, meshes, textures and building - Unions and collisions

Up: [[Models, meshes, textures and building]] · Previous: [[Models, meshes, textures and building - Poly budgets]] · Next: [[Models, meshes, textures and building - Welding and anchoring]]

- Unions can merge overlapping or z-fighting parts.
- The majority view is to avoid more than 1–2 unions. They don't work with CollectionService, and many precise unions lag.
- Alternative: union, export as OBJ, re-import as a mesh.
- Negated-union collisions are imprecise; build 4 walls and union them instead.
