---
tags: [huds-guide, technical]
---

# Performance, loading and mobile optimization - Targets

Up: [[Performance, loading and mobile optimization]] · Next: [[Performance, loading and mobile optimization - Loading and streaming]]

| Metric | Target or benchmark | Confidence |
|---|---|---|
| Frame time | 16.66 ms at 60 Hz; 8.33 ms at 120 Hz | High |
| FPS | 60+ when optimized; ~47 is low; 20–30 looks slow | High |
| Client FPS seen as normal | ~50 on PC, 30–40 on mobile | Medium |
| Network received | Under ~100 KB/s; sent even less; ~120 already lags | Medium |
| Network red flags | 230 KB/s down and 60 KB/s up in a simple obby; 700/300 KB/s crashed a game with server-side effects | Medium |
| Client memory on mobile | ~1,000 MB is acceptable | Medium |
| Studio RAM | 8 GB minimum, 16 GB needed, 32 GB better | Medium |
| Visual effects (VFX) on screen | Over 40 kills FPS | Medium |
| Highlight instances | 255 limit, not 32 ([docs](https://create.roblox.com/docs/effects/highlighting)) | High |
| Loading screen length | ~2 minutes wrecks bounce rate once a game is in recommendations | Medium |

- A 60 FPS cap is fine.
- Frame drops are usually a game-optimization problem, not PC specs.
- Intermittent drops point to VFX; constant drops point to the map.
- Studio using 9–12 GB of RAM is normal, not a leak.
- Low-end device performance is Roblox's core advantage; competitors' engines lag even on top graphics cards (owner-recommended).
- Programmers and builders should optimize together; most gains come from the map.

---
Related concepts: [[Bounce Rate]] · [[Test Bursts and Spikes]]
