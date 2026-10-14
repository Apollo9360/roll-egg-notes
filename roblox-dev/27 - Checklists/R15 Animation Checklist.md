---
title: R15 Animation Checklist
category: Checklists
tags: [checklist, animation, r15]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Animation checklist]
---

# R15 Animation Checklist

- [ ] The rig matches R15 naming and hierarchy; rest pose matches the Studio rig; armature object unscaled.
- [ ] Key poses blocked → breakdowns → polish; arcs checked; anticipation, follow-through and exaggeration present.
- [ ] Loops are seamless (first and last frames match); no bone flips.
- [ ] Export: one clip per FBX, Bake Animation on, no leaf bones, FBX Unit Scale (or the plugin route for Motor6D rigs).
- [ ] In Studio: no foot sliding; correct facing and scale; priority set (Idle/Movement for locomotion, Action+ for attacks and emotes); looping set.
- [ ] Event markers for gameplay sync (Hit, Footstep, Release).
- [ ] Published under the right creator (group for group games).
- [ ] Played with `Animator:LoadAnimation` (never `Humanoid:LoadAnimation`); tracks cached; player characters animated on the client, NPCs on the server or by local client crowd animation.

Details: [[R15 Blender Animation Pipeline]] · [[Roblox Animation Engineering]]

## Sources

[Character body specifications](https://create.roblox.com/docs/avatar/character-bodies/specifications), [Animator:LoadAnimation](https://create.roblox.com/docs/reference/engine/classes/Animator#LoadAnimation). AUTHORITATIVE.

## Last Verified

2026-10-14
