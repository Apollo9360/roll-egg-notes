---
title: R15 Animation Context
category: Context Packs
tags: [context-pack, animation, r15, blender, animator]
status: active
source_quality: Distilled from the linked vault notes (sources cited there)
last_verified: 2026-10-14
aliases: [Animation context]
---

# R15 Animation Context

## Summary

Load this before animating characters or NPCs, or writing code that plays animations.

## Load these notes, in order

1. [[R15 Blender Animation Pipeline]].
2. [[Roblox Animation Engineering]].
3. [[Combat Systems Engineering]] if the animation drives hits.

## Non-negotiables

- Play with `Animator:LoadAnimation(animation)` → `AnimationTrack:Play()`. `Humanoid:LoadAnimation` is deprecated.
- Load each animation once and reuse the track; `Animator:GetTrackByAnimationId()` finds one already loaded.
- A player's own character animations play on **that client** and replicate. Non-character rigs play from the **server**, with an Animator created on the server.
- Priority order: Core < Idle < Movement < Action < Action2 < Action3 < Action4. Set priority and looping before publishing.
- Sync gameplay to animation **event markers**, not guessed `task.wait` timings.
- Publish to the right owner (you or the group), or the game can't load it.

## Two routes from Blender

- **FBX:** Bake Animation on, then Animation Editor → ⋯ → Import → From FBX Animation onto a matching R15 rig. Works for skinned and custom rigs.
- **Rig-export add-on (community):** good for Motor6D rigs (R6/R15); no skinned meshes.

## Checklists to run

[[R15 Animation Checklist]] · [[Blender Export Checklist]]

## Related notes

- [[START HERE]]
- [[AI Roblox Development Playbook]]
- [[Roblox Development Golden Rules]]

## Sources

The notes linked above. Each cites its official sources.

## Last Verified

2026-10-14. Facts here are copied from the linked notes, which cite their sources. If a number here and a note disagree, the note wins; fix this pack.
