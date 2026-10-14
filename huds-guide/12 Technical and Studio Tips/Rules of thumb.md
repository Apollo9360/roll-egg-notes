---
tags: [huds-guide, technical]
---

# Rules of thumb

Up: [[Technical and Studio Tips]] · Previous: [[AI workflow, limits and learning to script]]

- Profile before optimizing: MicroProfiler for frames, Script Profiler for CPU, Memory view for leaks.
- If it works in Studio but not live, suspect streaming, unapproved assets or Studio-only behavior, then test in the real client.
- If it's slow in Studio but fine on a phone, trust the phone.
- Server decides, client displays: rolls, currency, purchases and hit validation belong on the server.
- Scale everything in UI, including strokes, padding and grid cells; Offset is the default cause of mobile breakage.
- Keep network receive under ~100 KB/s and on-screen VFX under ~40.
- Fewer textures, unions and visible triangles beat cleverer scripts for FPS.
- One soft shutdown beats several hard restarts; never privatize a live game.
- Let AI write code through Rojo; use MCP only for what files can't express.
- Read every line of AI-written save, purchase and remote code before release.
- Treat any free model script, plugin or "script file" from strangers as hostile until checked.

---
Related concepts: [[AI Tools]]
