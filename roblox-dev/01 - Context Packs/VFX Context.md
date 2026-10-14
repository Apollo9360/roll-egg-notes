---
title: VFX Context
category: Context Packs
tags: [context-pack, vfx, particles, beams, trails]
status: active
source_quality: Distilled from the linked vault notes (sources cited there)
last_verified: 2026-10-14
aliases: [Effects context]
---

# VFX Context

## Summary

Load this before building effects: hits, pickups, hatches, auras, trails.

## Load these notes, in order

1. [[VFX Production Guide]].
2. [[Decision - ParticleEmitter vs Beam vs Trail]].
3. [[Decision - When to Use Object Pooling]] and [[Recipe - Object Pool]].
4. [[Lighting and Environment Guide]] (bloom and colour) and [[Audio Design and Implementation]] (every big effect has a sound).

## Non-negotiables

- Layer it: core flash, a directional shape (beam, trail or mesh), secondary particles, a lingering tail.
- Time it: anticipation → impact → dissipation.
- Create effects on **clients**. The server sends "effect X at position Y".
- One-shots use `Emit(n)` bursts. Keep emitters few and short-lived; pool hot effects.
- Respect low-end devices: flipbooks switch off when a client is low on memory. Honour Reduce Motion for camera shake.

## Checked code

`VfxBurst` and `CameraShake` in [[VFX Production Guide]] and [[Camera Systems]].

## Checklists to run

[[VFX Checklist]] · [[Performance Checklist]]

## Related notes

- [[START HERE]]
- [[AI Roblox Development Playbook]]
- [[Roblox Development Golden Rules]]

## Sources

The notes linked above. Each cites its official sources.

## Last Verified

2026-10-14. Facts here are copied from the linked notes, which cite their sources. If a number here and a note disagree, the note wins; fix this pack.
