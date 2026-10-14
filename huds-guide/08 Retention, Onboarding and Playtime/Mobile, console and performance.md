---
tags: [huds-guide, retention]
---

# Mobile, console and performance

Up: [[Retention, Onboarding and Playtime]] · Previous: [[Playtime levers and their limits]] · Next: [[Multiplayer, PvP and empty servers]]

- Roughly 75–80% of Roblox's player base is on mobile.
- Mobile UX (user experience) friction is the most likely cause of tutorial funnel losses.
- Mobile playtime often runs 25–40% below PC and console, and broken controls can widen this tenfold.
- Test in Studio's device emulator, for example as an iPhone 13.
- Early mobile leaves come from "left click" prompts, buttons that silently fail and broken UI scaling.
- Tutorial UI covering half of a phone screen kills completion; remove billboard instructions on mobile.
- Force landscape mode on mobile.
- Low playtime with otherwise good stats can mean the main mechanic lags on phones and tablets.
- Mobile ping averages 100–300 ms per Roblox docs, and is worse from regions such as South Africa.
- Turn on console support; console players play much longer (owner-recommended).
- Console playtime collapses, such as 3 minutes versus 20, when controls break; complex combat games need real controller support.

### Loading and network

- Loading 11 worlds without StreamingEnabled caused 20–40 second first loads on mobile; load the current world and stream the rest.
- Loading screens should initialize core data only; preload only what players notice missing, such as HUD (heads-up display) buttons.
- Batch preloads and lazy-load the rest; over-using PreloadAsync causes FPS (frames per second) drops and join micro-freezes.
- Clearing about 20k textures took one game from 40 to 200 FPS.
- 600 KB/s receive and 300 KB/s send is unplayable on weak connections.
- Typical causes are a RunService module replicating constantly or remotes firing every frame.
- Use unreliable remotes plus client interpolation.

### FPS targets

| FPS | Reading | Confidence |
|---|---|---|
| 60+ | Well optimized | High |
| About 47 | Somewhat low | Medium |
| 20–30 | Skipping frames, looks slow | Medium |

- Intermittent drops point to VFX; constant drops point to the map.
- Performance is not everything: one poorly optimized game still hit 92nd+ percentile on playtime, D1 and D7 (low confidence).

---
Related concepts: [[Playtime]] · [[Onboarding and Tutorials]] · [[D1 Retention]] · [[D7 Retention]] · [[Funnels]]
