---
title: Combat Systems Engineering
category: Combat
tags: [combat, hitbox, raycast, shapecast, melee, projectiles, cooldowns, damage, server-authority, game-feel]
status: active
source_quality: AUTHORITATIVE (security and spatial-query docs) + labelled practice; code type-checked
last_verified: 2026-10-14
aliases: [Melee hitbox, Projectile system, Hit validation, Damage pipeline, Hit stop, Combat feel, Server cooldown]
---

# Combat Systems Engineering

## Summary

**Combat = client feel + server truth.**
- **The client** predicts and presents instantly: animation, VFX, sound, camera, hit markers.
- **The server** decides with **server-side stats**:
  - weapon damage, range, cooldown and ammo;
  - **server-side positions**;
  - server spatial queries (raycast, shapecast, overlap);
  - validation of client hit claims, with latency tolerance.

Never accept "I dealt X damage to Y".

## Spatial query toolbox

All on `workspace` (WorldRoot), all official:

| Query | Use |
|---|---|
| `Raycast(origin, direction, RaycastParams)` | Hitscan guns, line of sight, ground checks |
| `Blockcast` / `Spherecast` / `Shapecast` | Swept volumes: thick bullets, sword arcs, dashes. These catch hits a thin ray misses. |
| `GetPartBoundsInBox` / `GetPartBoundsInRadius` | Fast overlap by bounding box: melee hitboxes, AoE |
| `GetPartsInPart` | Exact overlap with a part's geometry, for odd-shaped hitboxes |
| `RaycastParams` / `OverlapParams` | `FilterType` (Include or Exclude), `FilterDescendantsInstances`, `CollisionGroup`, `RespectCanCollide` |

`Touched` is unreliable for damage: client-owned parts can fake or suppress it ([[Network Ownership and Physics Security]]).

## Architectures

| Combat type | Recommended flow |
|---|---|
| **Melee** | The client plays the swing and sends `RequestSwing(weaponId)`. The server checks its cooldown, waits the server-side wind-up, then runs an overlap box in front of the **server** position, filters out self and teammates, and applies server damage once per target. |
| **Hitscan** | The client raycasts and shows the result immediately, then sends `(origin, direction, claimedHitPart)`. The server checks fire rate, ammo, origin near the shooter (with tolerance), that the claimed hit is near that part's server position, and that **no static geometry** blocks the line. Only then does it apply damage. |
| **Projectiles** | The server owns the authoritative simulation: spawn time, velocity, a stepped raycast or shapecast per tick. Clients render a **local visual projectile** from the same parameters, so nothing replicates per frame. For fast projectiles, use a client-claimed hit validated like hitscan. |
| **Competitive or physics combat** | Consider the **Server Authority** model: prediction and rollback, beta as of 2026-10 ([[Network Ownership and Physics Security]]). |

## Damage pipeline

A single server module owns all damage:

```text
request → validate (rate, type, context) → compute base damage from SERVER stats
→ modifiers (crit, armor, buffs/debuffs, status effects) → clamp → apply → record (last hitter, combat log)
→ notify clients (damage numbers, hit reaction) → death handling (once!)
```

- **One function, `applyDamage(attacker, target, source)`.** It is the only code that changes health, so it is easy to audit and balance.
- **Status effects:** a server-side list per target of `{ kind, until, stacks }` ticked at a fixed rate (not per frame), with effects replicated as attributes for UI and VFX.
- **Knockback:** applied by the owner of the target's physics. For player characters, the client owns them, so send an impulse request the client applies, or use the server-authority model. For NPCs, apply it on the server.
- **Death:** guard against double death, e.g. `if humanoid.Health <= 0 then return end` before applying. Award kills exactly once.

## Code: server melee

<!-- code: ServerScriptService/Services/MeleeService.luau -->
```lua
--!strict
-- ServerScriptService/Services/MeleeService (ModuleScript, server only)
-- Server-authoritative melee: the client only says "I swung". The server owns
-- weapon stats, cooldowns, the hitbox query, damage and the hit list.
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")

type WeaponStats = { damage: number, cooldown: number, windup: number, range: number, width: number }

-- Server-side weapon table. Clients never send damage or range.
local WEAPONS: { [string]: WeaponStats } = {
	Sword = { damage = 20, cooldown = 0.6, windup = 0.15, range = 6, width = 5 },
}

local lastSwing: { [Player]: number } = {}

local function getRoot(model: Model?): BasePart?
	if not model then
		return nil
	end
	local root = model:FindFirstChild("HumanoidRootPart")
	return if root and root:IsA("BasePart") then root else nil
end

local function getAliveHumanoid(model: Model): Humanoid?
	local humanoid = model:FindFirstChildOfClass("Humanoid")
	if humanoid and humanoid.Health > 0 then
		return humanoid
	end
	return nil
end

local MeleeService = {}

-- Called from the validated remote handler after rate limiting.
function MeleeService.swing(player: Player, weaponName: string)
	local stats = WEAPONS[weaponName]
	local character = player.Character
	local root = getRoot(character)
	if not stats or not character or not root or not getAliveHumanoid(character) then
		return
	end
	-- Server cooldown: the authoritative one. The client cooldown is only UX.
	local now = os.clock()
	if now - (lastSwing[player] or 0) < stats.cooldown then
		return
	end
	lastSwing[player] = now

	-- Wind-up runs on the server too, so a client can't skip the telegraph.
	task.wait(stats.windup)
	root = getRoot(player.Character)
	if not root or player.Character ~= character or not getAliveHumanoid(character) then
		return -- died, respawned or left during the wind-up
	end

	-- Hitbox: a box in front of the attacker, using the SERVER's view of positions.
	local boxCFrame = root.CFrame * CFrame.new(0, 0, -stats.range / 2)
	local params = OverlapParams.new()
	params.FilterType = Enum.RaycastFilterType.Exclude
	params.FilterDescendantsInstances = { character }
	local parts = Workspace:GetPartBoundsInBox(boxCFrame, Vector3.new(stats.width, 5, stats.range), params)

	local hitModels: { [Model]: boolean } = {} -- one hit per target per swing
	for _, part in parts do
		local model = part:FindFirstAncestorOfClass("Model")
		if model and not hitModels[model] then
			local humanoid = getAliveHumanoid(model)
			local targetPlayer = Players:GetPlayerFromCharacter(model)
			local sameTeam = targetPlayer ~= nil and targetPlayer.Team ~= nil and targetPlayer.Team == player.Team
			if humanoid and not sameTeam then
				hitModels[model] = true
				humanoid:TakeDamage(stats.damage)
			end
		end
	end
end

function MeleeService.start()
	Players.PlayerRemoving:Connect(function(player)
		lastSwing[player] = nil
	end)
end

return MeleeService
```
<!-- /code -->

Wire `MeleeService.swing` to a `RequestSwing` RemoteEvent through the rate-limited handler pattern in [[Recipe - Secure Remote Handler]].

## Game feel

Common action-game practice; tune by playtesting:
- **Anticipation → impact → recovery** in the animation. Hit frames are driven by animation markers on the client ([[Roblox Animation Engineering]]).
- **Hit-stop:** freeze or slow the attacker's and victim's animations for about 50–100 ms on a heavy hit (`AnimationTrack:AdjustSpeed(0)`, then restore).
- **Feedback stack on the same frame:**
  - flash or highlight on the target;
  - sparks ([[VFX Production Guide]]);
  - impact sound with pitch variation;
  - small camera shake ([[Camera Systems]]);
  - damage number.
- **Telegraphs** for enemy attacks: wind-up pose, ground decal or glow. Players must be able to read and dodge them.
- **Cooldown UI** from server-confirmed state, with client prediction for responsiveness.

## PvE vs PvP

- **PvE** can be more forgiving: the server simulates NPCs, and clients get generous hit tolerance.
- **PvP** needs consistent latency handling. Validate with tolerance rather than strict checks, consider server authority, and log suspicious patterns ([[Remote Security and Anti-Exploit]]).

## Common mistakes

- The client sends a damage amount or target list.
- Damage via `Touched` on a client-owned weapon.
- Cooldowns enforced only on the client.
- Hitboxes evaluated on the client's positions without any server check.
- Hitting the same target several times in one swing.
- Server-side VFX for every hit.

## Related notes

- [[Remote Security and Anti-Exploit]]
- [[NPC and AI Engineering]]
- [[Roblox Animation Engineering]]
- [[VFX Production Guide]]
- [[Camera Systems]]

## Sources

AUTHORITATIVE:
- [Securing the client-server boundary: weapon targeting](https://create.roblox.com/docs/scripting/security/client-server-boundary)
- [Raycasting](https://create.roblox.com/docs/workspace/raycasting)
- [WorldRoot API (Shapecast, GetPartBoundsInBox …)](https://create.roblox.com/docs/reference/engine/classes/WorldRoot)
- [Parallel Luau: server-side raycasting validation example](https://create.roblox.com/docs/scripting/multithreading)

Game-feel practice: industry action-game conventions. USEFUL.

## Last Verified

2026-10-14
