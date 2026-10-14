---
title: R15 Blender Animation Pipeline
category: Animation
tags: [animation, blender, r15, rig, ik, fk, fbx, animation-editor, pipeline]
status: active
source_quality: AUTHORITATIVE (rig specs, Studio import) + COMMUNITY (Blender add-ons) — labelled per step
last_verified: 2026-10-14
aliases: [Blender R15 animation, Animate R15 in Blender, R15 rig Blender, Import FBX animation, RbxAnimations]
---

# R15 Blender Animation Pipeline

## Summary

There are two working routes from Blender keyframes to a Roblox `Animation` asset:

**A. FBX route (official Studio feature).**
1. Animate an R15-compatible armature in Blender.
2. Export FBX with **Bake Animation on**.
3. In Studio's **Animation Editor**, use **⋯ → Import → From FBX Animation** onto a matching R15 rig.
4. Publish.

Best for skinned and custom rigs. Watch for scale and axis issues.

**B. Rig-export add-on route (community).**
1. A Studio plugin exports the Roblox rig to Blender as an armature.
2. You animate it.
3. The Blender add-on produces keyframe data that the Studio plugin imports back.

This avoids FBX coordinate problems and suits **Motor6D rigs (R6/R15)**. It does **not** support skinned meshes.

Whichever route you use, finish in Studio:
- set the **priority** and **looping**;
- add **event markers**;
- publish the animation to the right owner (you or the group).

## Step 1 — Get a correct R15 rig into Blender

Official R15 requirements (`avatar/character-bodies/specifications`):
- **15 parts and bones:** LowerTorso (root of the body), UpperTorso, Head, Left/Right UpperArm, LowerArm and Hand, Left/Right UpperLeg, LowerLeg and Foot.
- **Hierarchy:** Root → LowerTorso → UpperTorso → Head, arms; LowerTorso → legs.
- **Root and LowerTorso** at 0,0,0. Bones frozen (scale 1, rotation 0). At most 4 influences per vertex; no influence on Root.
- **Export pose:** I-, A- or T-pose.
- **Advanced R15:** up to 37 extra optional bones (fingers, shoulders, spine). Each must follow the naming and hierarchy rules.
- **Template:** Roblox provides a downloadable rig-and-attachment template `.blend` with a standard R15 rig. See "Rig a humanoid model".

Getting a rig to animate:
- **For skinned or custom characters:** rig them to that template, skin them, and import them to Studio with the Importer, which creates bones and skinning.
- **For standard Motor6D avatars** (the usual R15 player character): export the rig with the community plugin (route B), or rebuild it from the template.

## Step 2 — Set up for animating (common practice)

- **IK/FK controls.** A control rig makes posing practical: IK legs for planted feet, FK arms for arcs, an IK/FK switch for hands holding weapons. Community R15 rigs with IK/FK switching exist for Blender 4.x (DevForum "R15 Rig for Blender 4.0+ (FK/IK switching)"). Check that a downloaded rig's bone names still match R15 **before** animating.
- **Frame rate.** Roblox animations are keyframe-based. Animate at your target rate (30 or 60) and keep keys on whole frames.
- **Scale.** Keep the rig at Roblox scale with the Blender units set up as in [[Blender to Roblox Modeling Pipeline]]. Don't scale the armature object; scale problems are the top cause of broken imports.

## Step 3 — Animate well

Classic principles, adapted to Roblox's distant, chunky characters:

| Principle | Roblox application |
|---|---|
| **Pose to pose**: block key poses first, then breakdowns, then polish | Readable silhouettes at distance matter more than subtle in-betweens |
| **Anticipation** | A short wind-up before attacks (2–6 frames at 30 fps) telegraphs the move to opponents and sells the force |
| **Timing and spacing** | Fast actions: few frames, big spacing. Heavy actions: hold the anticipation, snap the hit, recover slowly. |
| **Arcs** | Hands, weapon tips and heads move in arcs. Check them with motion paths in Blender. |
| **Follow-through and overlap** | Arms, tails and accessories settle after the body stops. With a Motor6D rig, overlap must be keyed. |
| **Exaggeration** | Push poses further than feels natural. R15 proportions and distance flatten motion. |
| **Ease in and out** | Use Graph Editor curves. Bezier for organic motion; linear or constant only where you want a mechanical or snappy feel. |
| **Loop cleanly** | First and last frames identical for idles, walks and runs. Cycle the F-curves (Cycles modifier) while working. |

Graph Editor and Dope Sheet: clean curves, remove redundant keys, check that no bone flips (quaternion vs Euler rotation).

## Step 4 — Export

**Route A, FBX:**
- Use the base settings from [[Blender to Roblox Modeling Pipeline]] (FBX Unit Scale, Add Leaf Bones **off**), but set **Bake Animation on**.
- **Only one animation per FBX** (official spec). Export each clip separately.
- Export **only the armature** (plus meshes if you're importing a skinned character).

**Route B, add-on:**
- Use the add-on's export to clipboard or file.
- Paste or import in the Studio plugin.

## Step 5 — Import into Studio and publish

1. Open the Animation Editor on a rig whose bones match.
2. **⋯ → Import → From FBX Animation** (route A, the official path documented for Maya exports; it works the same for Blender FBX), or use the plugin's import (route B).
3. **Check** that feet stay planted with no sliding, nothing flips, the scale is right, and loops are seamless.
4. **Set the priority:** Core < Idle < Movement < Action < Action2 < Action3 < Action4.
   - Idle, walk and run: Idle or Movement.
   - Attacks and emotes: Action and up.

   Higher priorities override lower ones on the joints they animate.
5. **Looping** on for cycles.
6. **Add event markers** (e.g. `Hit`, `Footstep`, `Release`) for gameplay sync; see [[Roblox Animation Engineering]].
7. **Publish to Roblox.** For group games, set **Creator = the group**, or the game can't load it.
8. **Replacing a default** run or jump animation requires matching naming. See "Use animations" in the official docs.

## Troubleshooting

| Symptom | Likely cause and fix |
|---|---|
| Rig explodes or limbs rotate wildly | Bone roll or rest pose differs from the Studio rig. Re-export the rig from Studio (route B), or match the template rest pose. |
| Animation is tiny or huge, or limbs stretch | Scale mismatch. Apply scale, use FBX Unit Scale, and never scale the armature object. |
| Character faces the wrong way | Axis settings: Forward/Up in the export, Front/Top in the import. |
| Frozen or no motion after FBX import | Bake Animation was off; only the first action was exported; or bone names don't match the rig |
| Works in Studio, not in game | Animation owned by the wrong creator (user vs group); or loaded on a client-created Animator ([[Roblox Animation Engineering]]) |
| Weapon not in hand | Weapon held by a Motor6D or RigidConstraint to the hand; animate that joint (see below) |

## Weapons, two-handed props, mounts

- **One-handed weapon:** a Motor6D from RightHand to the weapon's handle, created by script when equipped. Animate it as an extra joint so the weapon can swing independently of the wrist.
- **Two-handed:** parent the weapon to one hand. Bring the other hand to it with IK in Blender (baked), or at runtime with `IKControl`.
- **Mounts and vehicles:** a seat plus a dedicated sitting or riding animation at Action priority. Sync the mount's own AnimationController animation with the rider's state changes.
- **NPC rigs:** an AnimationController with an Animator (no Humanoid) for static or custom-moving NPCs, which is cheaper. See [[NPC and AI Engineering]].

## Related notes

- [[Roblox Animation Engineering]]
- [[Blender to Roblox Modeling Pipeline]]
- [[R15 Animation Checklist]]
- [[R15 Animation Context]]

## Sources

AUTHORITATIVE:
- [Character body specifications: R15 rigging](https://create.roblox.com/docs/avatar/character-bodies/specifications)
- [Rig a humanoid model (Blender, template)](https://create.roblox.com/docs/art/modeling/rig-a-humanoid-model)
- [Export settings (single animation per FBX)](https://create.roblox.com/docs/art/modeling/specifications)
- [Export avatar animations from Maya (Import from FBX Animation steps)](https://create.roblox.com/docs/art/characters/export-avatar-animations-from-maya)
- [Beyond the Dark: custom characters (rig and skin rules)](https://create.roblox.com/docs/resources/beyond-the-dark/custom-characters)
- [Enum.AnimationPriority](https://create.roblox.com/docs/reference/engine/enums/AnimationPriority)

COMMUNITY (not verified first-hand; check versions before use):
- [DevForum: Importing Blender rigs/characters and animations to Roblox](https://devforum.roblox.com/t/importing-blender-rigscharacters-and-animations-to-roblox/2044533): the Den_S "RbxAnimations" add-on plus Studio plugin; skinned meshes unsupported.
- [DevForum: R15 Rig for Blender 4.0+ (FK/IK switching)](https://devforum.roblox.com/t/r15-rig-for-blender-40-fkik-switching/3606878)
- [DevForum: animating a skinned-mesh R15 in Blender](https://devforum.roblox.com/t/how-can-i-animate-skinned-mesh-r15-in-blender-and-import-into-studio/2962873): users report FBX animation import as sometimes unreliable.
- [Nilo: Export Blender animations to Roblox](https://nilo.io/articles/export-animation-blender-roblox) (vendor article): recommends Bake Animation, no leaf bones, FBX unit scale.

## Last Verified

2026-10-14. Video tutorials with timestamps are not yet harvested; see [[Research Backlog]].
