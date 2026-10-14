---
title: Decision - Polling vs Events
category: Decision Guides
tags: [decision, performance, luau]
status: active
source_quality: AUTHORITATIVE facts + labelled judgement
last_verified: 2026-10-14
aliases: [Polling vs Events, RunService vs events]
---

# Decision - Polling vs Events

**Prefer events:**
- `Changed` and `GetPropertyChangedSignal`;
- `GetAttributeChangedSignal`;
- `CollectionService` added/removed signals;
- `ChildAdded`;
- `Humanoid.Died`;
- remote messages;
- `task.delay` for timers.

**Use per-frame (RunService) only when the value truly changes every frame and you must react every frame:**
- camera;
- visual interpolation and springs;
- custom character motion;
- aim.

**Use a low-rate loop** (`task.wait(0.1–1)`) when:
- you aggregate many objects (an NPC brain at 5 Hz, zone checks);
- or no event exists for what you need. Stagger loop start times.

| Situation | Choose |
|---|---|
| HUD shows coins | Attribute changed signal |
| Door opens when a player is near | ProximityPrompt, or a zone check at 5–10 Hz |
| Smooth follow camera | RenderStepped / BindToRenderStep |
| Hatch timer UI | Compute from a timestamp each frame **only while visible**, or once per second |
| Server cooldown | Timestamp comparison at request time; no loop at all |

Why: at 60 FPS each frame has 16.67 ms. Small per-frame costs multiplied by many objects drain it ([[Performance and Profiling Guide]]).

## Sources

[Design for performance](https://create.roblox.com/docs/performance-optimization/design), [Improve performance](https://create.roblox.com/docs/performance-optimization/improve). AUTHORITATIVE.

## Last Verified

2026-10-14
