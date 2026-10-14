---
tags: [huds-guide, technical]
---

# Studio setup, debugging and publishing - Debug tools

Up: [[Studio setup, debugging and publishing]] · Next: [[Studio bugs and fixes]]

| Tool | How to open | Use |
|---|---|---|
| MicroProfiler | Ctrl+F6 (Ctrl+P pauses) | Frame times, lag spikes, loading spikes, script-heavy interactions |
| Server command bar | F9, then Server tab | Run server code while in client view |
| Performance stats | Ctrl+F1 / Ctrl+F2 / Shift+F3 | Triangle count on screen, network KB/s |
| Dev Console Memory view | F9, then Memory | Memory breakdown per category |
| Scene Analysis | Studio | Shadows, meshes and memory per script |
| Script Profiler | Studio | Processor (CPU) spikes per script |
| Monitoring, then Performance | Creator Dashboard | Live client frames per second (FPS) and crash data |
| Editor Quality Level | File, Studio Settings (Alt+S), Rendering | Set to 21 for clean model screenshots |

- For CPU spikes every 10–15 s, use the MicroProfiler and Script Profiler rather than asking AI to optimize blindly.
- Measure memory and load times in the real client. Studio adds overhead; one place used 12 GB in Studio versus 1.6 GB live.
- A 100 ms freeze on spawn in Studio did not occur on a real phone, so Studio lag isn't representative.
- The script source size limit is 200k characters.
- For mobile checks, the iPhone 13 Pro device emulator is the common default.
- Playtesting opens a separate window while the main Studio stays in edit mode, so an AI agent can keep editing.

---
Related concepts: [[Test Bursts and Spikes]] · [[AI Tools]]
