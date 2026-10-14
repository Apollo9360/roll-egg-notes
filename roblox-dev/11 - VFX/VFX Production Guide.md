---
title: VFX Production Guide
category: VFX
tags: [vfx, particles, beams, trails, flipbooks, lightemission, zoffset, impact, aura, loot, recipes]
status: active
source_quality: AUTHORITATIVE (emitter, beam and trail behaviour) + labelled craft principles
last_verified: 2026-10-14
aliases: [Particles, ParticleEmitter, Beams, Trails, Flipbooks, Hit effects, Sword Slash VFX Recipe, Impact Burst Recipe, Loot Beam Recipe, Magical Aura Recipe, Explosion Layering Recipe, Rare Item Reveal Recipe]
---

# VFX Production Guide

## Summary

Good Roblox VFX are **layered**, **timed** and **readable**. A strong effect is several simple layers:
- a bright core flash;
- a shape that sells direction (beam, trail or mesh);
- secondary particles (sparks, debris);
- a lingering tail (smoke, glow fade).

Each layer has its own timing: **anticipation → impact → dissipation**.

**Engineering rules:**
- Create effects **on clients**. The server only says "effect X at position Y".
- Use **`Emit(n)`** bursts for one-shots.
- Keep emitters few and short-lived, and pool hot effects.
- Respect low-end devices: flipbooks switch off when a client is low on memory.

## The tools

| Tool | What it does | Key properties (official) |
|---|---|---|
| `ParticleEmitter` | 2D sprites emitted from a part's volume or shape, or from an Attachment's point | `Texture`, `Color` (ColorSequence), `Size` and `Transparency` (NumberSequence over lifetime), `Lifetime`, `Speed`, `Rate`, `SpreadAngle`, `Acceleration`, `Drag`, `Rotation`/`RotSpeed`, `Orientation`, `LightEmission`, `LightInfluence`, `Brightness`, **`ZOffset`**, `Squash`, `Shape`/`ShapeStyle`/`ShapeInOut`/`ShapePartial`, flipbooks, `TimeScale`, `LockedToPart`, `WindAffectsDrag`; method `Emit(count)`, `Clear()` |
| `Beam` | Texture stretched between two Attachments, with curve (`CurveSize0/1`), width (`Width0/1`), segments, texture length and mode, and scroll speed | Lasers, loot pillars, tethers, waterfalls, slash arcs |
| `Trail` | Ribbon left behind two moving Attachments over `Lifetime` | Sword swings, projectile streaks, dash and footprint marks |
| Lights (`PointLight`, `SpotLight`, `SurfaceLight`) | Real light | Brief flashes only; costly, and shadows cost more |
| Mesh VFX (MeshPart with a texture and Transparency tweens) | Shockwave rings, slash crescents, shields | Neon or textured meshes scaled and faded by tween |
| `Highlight` | Outline or fill on a model | Selection, damage flash, rarity glow |

### Particle details

All from the official docs:
- **Parenting:** under a **BasePart**, particles spawn anywhere in its volume, and `EmissionDirection` picks the face. Under an **Attachment**, they spawn at a point; rotate the attachment to aim them.
- **`LightEmission = 1`** blends additively (glow), and **hides dark regions** of grayscale textures without alpha. Use it for fire, magic and energy. Smoke and dust need 0 and real alpha.
- **`ZOffset`** pushes rendering towards or away from the camera **without changing on-screen size**. Use it to layer emitters (core in front of smoke) or to draw in front of the parent.
- **Flipbooks:**
  - layouts Grid2x2, 4x4, 8x8 (a 1024² 8×8 sheet is 64 frames) or Custom;
  - modes Loop, **OneShot** (frames spread over the lifetime), PingPong, Random;
  - `FlipbookFramerate` up to **30 fps**;
  - `FlipbookStartRandom`.
  - **Leave transparent spacing between frames** so mip filtering doesn't bleed.
  - **Clients disable flipbooks under memory pressure** (likely on older phones), so effects must still read without them.
- **Check every effect at the lowest and highest Editor Quality Level.** Particle quality varies by device graphics setting.
- **Performance:** decals, textures and particles batch poorly. **Changing ParticleEmitter properties at runtime can hurt performance badly** (official). Prefer pre-authored variants and `Emit`.

## Craft principles

Industry practice, not Roblox rules:
1. **Read the gameplay first.** An effect's job is information: what happened, where, how strong, to whom. A hit must be readable inside a crowd fight.
2. **Shape language and colour language.** Assign colours by meaning and keep them consistent: element (fire orange, ice cyan), team, rarity (common grey → legendary gold, mythic rainbow), heal green, damage red.
3. **Timing.**
   - Anticipation is a short build (charge glow, gathering particles).
   - Impact is the brightest, largest frame, often 1–3 frames of flash.
   - Dissipation is a slower fade with lingering smoke or sparks.

   Fast in, slow out.
4. **Value contrast.** A bright core on a dark smoke edge reads at a distance. All-bright effects turn into mush.
5. **Exaggerate scale and contrast** for Roblox distances and phone screens.
6. **Layering:**
   - core (high LightEmission, short life);
   - body (shape, beam or mesh);
   - secondary (sparks with drag, gravity via `Acceleration`);
   - tail (smoke with LightEmission 0, longer life, growing size, fading transparency).
7. **Sync with SFX, animation and camera** for impact: hit-stop plus shake plus flash plus sound on the same frame ([[Combat Systems Engineering]], [[Camera Systems]]).

## Recipes

These are starting points. Tune by eye on target devices; the numbers are illustrative, not official.

### Impact Burst (hit spark)

- **Flash:** 1 particle, size about 3 → 0, lifetime 0.08–0.12, LightEmission 1, ZOffset 1.
- **Sparks:** 8–15 particles, speed 15–30, Drag 5–8, Acceleration (0, −30, 0), size 0.3 → 0, lifetime 0.2–0.4, SpreadAngle 180, Orientation VelocityParallel (streaks).
- **Dust puff:** 3–5 particles, LightEmission 0, size 1 → 3, Transparency 0.4 → 1, lifetime 0.4–0.6.
- **Plus:** a 0.1 s PointLight flash, a short camera-shake trauma (0.15), and an impact sound with pitch variation.

### Sword Slash

- A `Trail` between two Attachments along the blade: Lifetime about 0.15–0.25, Transparency 0 → 1, Width from attachment spacing, an additive texture with LightEmission 1.
- Optionally a crescent **mesh** slash scaled up and faded over 0.15 s for weight.
- Enable the trail only during the swing (animation markers `SwingStart`/`SwingEnd`). Disabled trails don't draw new segments.

### Loot Beam (rarity pillar)

- A vertical `Beam` from the ground attachment up to a high attachment. Colour by rarity, a TextureSpeed scroll, Transparency fading near the top.
- Sparkles around the base (Rate 5–10). Bigger and brighter for rarer items.
- Add a Highlight on the item for legendary and above.

### Magical Aura (buff, rarity glow)

- Attachment(s) on the HumanoidRootPart or the item:
  - slow orbiting sparkles (`LockedToPart`, low speed, `RotSpeed`);
  - a soft ground glow (a flat particle or a decal ring);
  - an occasional rising mote.
- Keep emission low; auras run continuously, so their cost is permanent.
- For a measured example of rarity aura layering (sparkles, circles, flare, rays, sunburst per tier), see this repo's `skills/roll-egg-art-direction/SKILL.md`.

### Explosion Layering

In order:
1. Pre-flash.
2. Fireball (flipbook or LightEmission 1, fast growth).
3. Shockwave ring (a mesh ring scaling out on the ground).
4. Debris and sparks (with gravity).
5. Smoke column (dark, slow, long life).
6. Scorch decal (removed after a few seconds).

Add camera shake scaled by distance, and an audio layer of thump, crack and tail.

### Rare Item Reveal

- Anticipation: the item shakes, glow builds, a rising pitch sound.
- Burst: flash, rays (a rotating radial texture), confetti or sparkles.
- Item presentation: scale pop with UIScale or a mesh tween, a rarity-coloured beam behind it, a rarity title in UI.
- Linger: slow sparkles.
- **The duration scales with rarity**, so common items stay fast and don't waste the player's time. Respect Reduce Motion for the UI parts.

## Code: playing authored bursts

<!-- code: ReplicatedStorage/Client/VfxBurst.luau -->
```lua
--!strict
-- ReplicatedStorage/Client/VfxBurst (ModuleScript, client)
-- Plays an authored one-shot effect (impact, loot pop, level-up) at a position.
-- Author the effect once as an Attachment holding ParticleEmitters / Beams / Lights
-- with Enabled = false; give each emitter an "EmitCount" attribute (default 10).
-- The server only says WHERE/WHAT (one small remote); clients make the visuals.
local Workspace = game:GetService("Workspace")

local VfxBurst = {}

local holder: Part? = nil
local function getHolder(): Part
	-- One invisible anchored part holds every burst attachment in world space.
	if holder and holder.Parent then
		return holder
	end
	local part = Instance.new("Part")
	part.Name = "VfxHolder"
	part.Anchored = true
	part.CanCollide = false
	part.CanQuery = false
	part.CanTouch = false
	part.Transparency = 1
	part.Size = Vector3.one
	part.CFrame = CFrame.identity
	part.Parent = Workspace
	holder = part
	return part
end

-- template: an Attachment authored in ReplicatedStorage/Assets/VFX.
-- scale: multiplies particle sizes for bigger/rarer versions (1 = authored size).
function VfxBurst.play(template: Attachment, position: Vector3, scale: number?)
	local effect = template:Clone()
	effect.WorldPosition = position
	effect.Parent = getHolder()

	local longestLifetime = 0
	for _, child in effect:GetDescendants() do
		if child:IsA("ParticleEmitter") then
			if scale and scale ~= 1 then
				local keypoints = {}
				for _, kp in child.Size.Keypoints do
					table.insert(keypoints, NumberSequenceKeypoint.new(kp.Time, kp.Value * scale, kp.Envelope * scale))
				end
				child.Size = NumberSequence.new(keypoints)
			end
			local count = child:GetAttribute("EmitCount")
			child:Emit(if typeof(count) == "number" then count else 10)
			longestLifetime = math.max(longestLifetime, child.Lifetime.Max)
		elseif child:IsA("Light") then
			child.Enabled = true
			-- Short flash: lights are expensive, keep them brief.
			task.delay(0.12, function()
				child.Enabled = false
			end)
		end
	end
	-- Destroy after the last particle has died.
	task.delay(longestLifetime + 0.1, function()
		effect:Destroy()
	end)
end

return VfxBurst
```
<!-- /code -->

- **Server → client:** one `RemoteEvent` (or `UnreliableRemoteEvent` for cosmetic spam) carries `(effectId, position, scale)`.
- **Validate before relaying**, if a client triggered it ([[Remote Security and Anti-Exploit]]).
- **Pool** high-frequency effects ([[Recipe - Object Pool]]).

## Common mistakes

- Creating effects on the server, which replicates every instance and every property change.
- Continuous emitters left running offscreen, or never disabled after an ability ends.
- Dark textures with LightEmission 1 (they vanish), or no alpha with LightEmission 0 (black squares).
- One huge flipbook per effect, which costs memory and is disabled on low-memory phones anyway.
- Effects that hide the gameplay: a giant aura covering the player, or VFX blocking the target.
- Never testing at low graphics quality or on a phone.

## Related notes

- [[VFX Checklist]]
- [[Recipe - Object Pool]]
- [[Combat Systems Engineering]]
- [[Lighting and Environment Guide]]
- [[VFX Context]]

## Sources

- [Particle emitters](https://create.roblox.com/docs/effects/particle-emitters), [Beams](https://create.roblox.com/docs/effects/beams), [Trails](https://create.roblox.com/docs/effects/trails), [Light sources](https://create.roblox.com/docs/effects/light-sources), [Highlighting](https://create.roblox.com/docs/effects/highlighting), [Improve performance](https://create.roblox.com/docs/performance-optimization/improve). AUTHORITATIVE, 2026-10-13.
- Layering, timing and colour language are standard game-VFX practice. USEFUL; video tutorial harvesting with timestamps is pending in [[Research Backlog]].

## Last Verified

2026-10-14
