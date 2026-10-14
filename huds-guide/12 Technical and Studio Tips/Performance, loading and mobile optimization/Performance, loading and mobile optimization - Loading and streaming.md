---
tags: [huds-guide, technical]
---

# Performance, loading and mobile optimization - Loading and streaming

Up: [[Performance, loading and mobile optimization]] · Previous: [[Performance, loading and mobile optimization - Targets]] · Next: [[Performance, loading and mobile optimization - Late-loading UI images]]

- Players leaving at the very first tutorial step usually means lag or long loading.
- Loading 11 worlds without StreamingEnabled caused 20–40 s first loads on mobile. Load only the current world and stream the rest.
- Whether to use StreamingEnabled is disputed. Some reserve it for very large maps and hand-code streaming or object pooling otherwise. Others call it fine if the game holds 70+ FPS.
- Loading screens should initialize only core systems and preload only what players notice missing, such as heads-up display (HUD) buttons and beams.
- Missing preloads cause visible pauses; over-using ContentProvider:PreloadAsync freezes joins and drops FPS.
- Use lazy or staged loading to avoid a micro-freeze on join.
- With streaming on, parts or meshes vanishing for some players: check MeshPart.DoubleSided, anchoring and streaming settings.
- If something works in Studio but not live, temporarily disable StreamingEnabled to test.

---
Related concepts: [[Onboarding and Tutorials]]
