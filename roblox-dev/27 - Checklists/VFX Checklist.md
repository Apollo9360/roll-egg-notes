---
title: VFX Checklist
category: Checklists
tags: [checklist, vfx]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Effects checklist]
---

# VFX Checklist

- [ ] Created on clients; the server sends only an effect ID, position and scale (validated if client-triggered).
- [ ] Layered: core flash, body or shape, secondary particles, tail. Timing is fast in, slow out.
- [ ] Readable: colour language consistent (element, rarity, team); value contrast; doesn't hide gameplay.
- [ ] `Emit(n)` for bursts; continuous emitters disabled when their owner ends.
- [ ] LightEmission 1 only on glow textures; smoke and dust use real alpha.
- [ ] ZOffset used to layer emitters.
- [ ] Flipbooks have spacing between frames; the effect still reads if flipbooks are disabled (low memory).
- [ ] No runtime property thrashing on ParticleEmitters; hot effects pooled.
- [ ] Lights are short flashes; shadows off.
- [ ] Checked at the lowest and highest Editor Quality Level and on a phone.
- [ ] Synced with sound, animation markers and camera; Reduce Motion respected for shakes.

Details: [[VFX Production Guide]]

## Sources

[Particle emitters](https://create.roblox.com/docs/effects/particle-emitters), [Improve performance](https://create.roblox.com/docs/performance-optimization/improve). AUTHORITATIVE.

## Last Verified

2026-10-14
