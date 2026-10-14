---
tags: [huds-guide, technical]
---

# UI components, effects and polish - BillboardGui and SurfaceGui

Up: [[UI components, effects and polish]] · Previous: [[UI components, effects and polish - Figma workflow]]

- Use Scale on all children and TextScaled. Elements vanish if children are bigger than the billboard.
- Most advice: size the billboard in Offset for a constant on-screen size, since Scale shrinks with distance. A minority claimed the opposite (disputed).
- Stack labels in one BillboardGui: enlarge it, turn off ClipsDescendants, add a vertical UIListLayout.
- Set LightInfluence to 0 so sunlight or darkness doesn't affect it. This avoids AlwaysOnTop drawing over heads.
- SurfaceGuis going dark at angled camera views have no known fix.
- SurfaceGui text missing: set Adornee and the correct Face.
