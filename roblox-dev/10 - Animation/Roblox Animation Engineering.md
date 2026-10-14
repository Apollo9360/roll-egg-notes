---
title: Roblox Animation Engineering
category: Animation
tags: [animation, animator, animationtrack, priority, markers, replication, ik, npc]
status: active
source_quality: AUTHORITATIVE
last_verified: 2026-10-14
aliases: [Animator, AnimationTrack, Animation priorities, Animation markers, Animation replication, IKControl]
---

# Roblox Animation Engineering

## Summary

- Play animations with **`Animator:LoadAnimation(animation)` → `AnimationTrack:Play()`**. `Humanoid:LoadAnimation` is **deprecated**.
- **Load each animation once** and reuse the track. Every `LoadAnimation` call creates a new track; use `Animator:GetTrackByAnimationId()` to find one already loaded.
- **Replication:** play a player's **own character** animations on that player's **client**; they replicate. Play animations on **non-character** rigs from the **server**, and the Animator must have been **created on the server**.
- **Sync gameplay to animation events** (markers), not to guessed `task.wait` timings.

## Replication rules

From the API reference for `Animator:LoadAnimation`:

| Rig | Where to load and play | Why |
|---|---|---|
| A player's character (Animator under Humanoid or AnimationController) | **That player's client** | Client-started tracks replicate to the server and other clients |
| Any rig the client owns (e.g. via `SetNetworkOwner(player)`) | That client | Same rule; loading there also loads it for the server |
| NPCs and other non-character rigs | **Server**, so it replicates | Otherwise only the local client sees it |
| An Animator **created on a client** | Only that client | Its tracks **never replicate** |

- The Animator must be **in Workspace** before `LoadAnimation`, or it errors.
- **For many NPCs:** play animations on **clients** instead. Create the Animator client-side, accept that it won't replicate, and animate only NPCs near the player. This saves server CPU and bandwidth ([[Performance and Profiling Guide]]).

## Priorities and blending

- **`Enum.AnimationPriority`**, lowest to highest: **Core**, **Idle**, **Movement**, **Action**, **Action2**, **Action3**, **Action4**. A higher priority overrides lower ones on the joints it animates; untouched joints keep the lower animation.
- **Default Animate script** clips: idle, walk, run, jump, fall, climb, swim. Replace them via the character's `Animate` script values with matching names ("Use animations" doc). Idle variants are chosen by **Weight**.
- **Blending:** `track:Play(fadeTime, weight, speed)`, `track:AdjustWeight(w, fadeTime)`, `track:AdjustSpeed(s)`, `track:Stop(fadeTime)`. A short fade (about 0.1–0.2 s) avoids pops. That value is common practice, not official.

## Events (markers)

- In the Animation Editor, enable **Show Animation Events** and add named markers with an optional parameter string. Reuse the same event name (e.g. `Footstep`) for both feet.
- In code:
  ```lua
  track:GetMarkerReachedSignal("Hit"):Connect(function(param: string?) applyHitbox(param) end)
  ```
  Also `track.Ended`, `track.Stopped`, `track.DidLoop`.
- **Combat:** markers drive the **client's** effects and the hitbox **request** timing. The **server** still validates the timing against its own cooldowns and wind-ups ([[Combat Systems Engineering]]).

## Procedural animation and IK

- **`IKControl`** (built in) makes a chain reach or look at a target at runtime: planting feet on slopes, a hand on a weapon grip or door, a head looking at a target.
- **Procedural pose changes:** write `Motor6D.Transform` (applied on top of animation), **not** `C0`/`C1`. Changing C0/C1 and the avatar hierarchy at runtime triggers costly FastCluster rebuilds (official performance guidance).
- **VFX on skinned meshes:** bones move but MeshPart positions don't update for attachments outside them. Follow bones using `Bone.TransformedWorldCFrame` each frame for attached effects (Beyond the Dark pattern).

## Code pattern: a cached track per character

```lua
-- Client: cache tracks per Animator so LoadAnimation runs once per animation.
local trackCache: { [Animator]: { [string]: AnimationTrack } } = setmetatable({}, { __mode = "k" }) :: any

local function getTrack(animator: Animator, animation: Animation): AnimationTrack
	local tracks = trackCache[animator]
	if not tracks then
		tracks = {}
		trackCache[animator] = tracks
	end
	local track = tracks[animation.AnimationId]
	if not track then
		track = animator:LoadAnimation(animation)
		tracks[animation.AnimationId] = track
	end
	return track
end
```

## Common mistakes

- `Humanoid:LoadAnimation` (deprecated).
- Calling `LoadAnimation` every time an attack plays, which leaks tracks.
- Playing NPC animations from a client-created Animator and wondering why others can't see them.
- Playing the player's own character animations from the server, which adds latency and network cost.
- Hit timing done with `task.wait(0.3)` instead of a marker.
- An animation uploaded by the wrong owner, so it fails to load in a group game.

## Related notes

- [[R15 Blender Animation Pipeline]]
- [[Combat Systems Engineering]]
- [[NPC and AI Engineering]]
- [[R15 Animation Checklist]]

## Sources

AUTHORITATIVE:
- [Animator:LoadAnimation (replication rules, GetTrackByAnimationId)](https://create.roblox.com/docs/reference/engine/classes/Animator#LoadAnimation)
- [Use animations](https://create.roblox.com/docs/animation/using)
- [Animation events](https://create.roblox.com/docs/animation/events)
- [Inverse kinematics](https://create.roblox.com/docs/animation/inverse-kinematics)
- [Improve performance: Humanoids (Motor6D.Transform)](https://create.roblox.com/docs/performance-optimization/improve)
- [Humanoid:LoadAnimation (deprecated)](https://create.roblox.com/docs/reference/engine/classes/Humanoid#LoadAnimation)

## Last Verified

2026-10-14
