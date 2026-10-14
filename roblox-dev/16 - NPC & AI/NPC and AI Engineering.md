---
title: NPC and AI Engineering
category: NPC & AI
tags: [npc, ai, pathfinding, state-machine, behavior-tree, humanoid, performance, guards]
status: active
source_quality: AUTHORITATIVE (Pathfinding and performance docs) + labelled patterns; code type-checked
last_verified: 2026-10-14
aliases: [PathfindingService, NPC state machine, Enemy AI, Guard AI, Humanoid vs custom controller, Large NPC counts]
---

# NPC and AI Engineering

## Summary

- **The server owns NPC decisions and physics.** Set `SetNetworkOwner(nil)` on gameplay NPCs.
- **Run brains at a low tick rate** (e.g. 5–10 Hz), not every frame.
- **Use a state machine** (idle, patrol, chase, attack, return) for most enemies. Use behaviour trees or utility AI only when behaviour truly branches a lot.
- **Use `PathfindingService` for long routes** around obstacles, and direct `MoveTo` for short open chases.
- **Scale up** by:
  - disabling unused Humanoid states;
  - removing Humanoids from static NPCs;
  - animating on clients;
  - spawning near players;
  - pooling.

## Pathfinding

Official facts:
- `PathfindingService:CreatePath({AgentRadius, AgentHeight, AgentCanJump, AgentCanClimb, WaypointSpacing, Costs})` → `path:ComputeAsync(start, goal)` → check `path.Status` → `GetWaypoints()`. Waypoint `Action == Jump` means jump; `Label == "Climb"` marks TrussPart climbing.
- **Handle `path.Blocked`.** Recompute only if the blocked index is **ahead** of the current waypoint.
- **Modifiers:**
  - material **Costs** (e.g. avoid water or lava);
  - `PathfindingModifier` regions (named costs, or `PassThrough` to ignore an obstacle such as a door);
  - `PathfindingLink` for jumps, teleporters and boats.
- **Limits:**
  - the straight-line distance must be **3,000 studs or less**;
  - at most **20,000 nodes** (big mazes or open worlds fail early);
  - waypoints are limited to Y between −65,536 and 65,536;
  - fails if agent parameters can't fit the route.

  Break long trips into legs.
- **Streaming:** the server sees the full world, so pathfinding there is safe. Client-side pathfinding may target parts that aren't streamed in; use Persistent destination parts.
- **Debugging:** Visualization Options → **Navigation mesh** and **Pathfinding modifiers**.
- `Humanoid:MoveTo` times out (MoveToFinished fires with `false`) if the goal isn't reached in time. Re-issue it each tick while chasing.

## Behaviour architecture

| Pattern | Use when |
|---|---|
| **Finite state machine** | Most enemies, guards, pets, shopkeepers. Clear states, easy to debug. |
| **Behaviour tree** | Complex bosses with many conditional sub-behaviours, reused across NPC types |
| **Utility AI** (score each action, pick the highest) | Many competing goals (eat, flee, attack, gather); emergent behaviour |
| **Server-scripted sequence** | Boss phases and cutscenes: a timeline of attacks with telegraphs |

**Perception:**
- distance checks (cheap; use squared distance or `Magnitude` at the tick rate);
- line of sight with `Raycast` only when in range;
- **aggro tables** (threat per player), with a leash range back to the post;
- give-up rules, e.g. the gold safe line in a steal game.

## Code

### State machine (generic)

<!-- code: ServerScriptService/Shared/StateMachine.luau -->
```lua
--!strict
-- ServerScriptService/Shared/StateMachine (ModuleScript)
-- Tiny finite-state machine for NPCs, abilities and rounds.
-- Each state has optional enter/update/exit; transitions are explicit.
export type State<C> = {
	enter: ((context: C) -> ())?,
	update: ((context: C, dt: number) -> string?)?, -- return a state name to transition
	exit: ((context: C) -> ())?,
}

export type Machine<C> = {
	current: string,
	context: C,
	step: (self: Machine<C>, dt: number) -> (),
	transition: (self: Machine<C>, nextState: string) -> (),
}

local StateMachine = {}

function StateMachine.new<C>(states: { [string]: State<C> }, initial: string, context: C): Machine<C>
	assert(states[initial], `Unknown initial state {initial}`)
	local machine = { current = initial, context = context } :: Machine<C>

	function machine.transition(self: Machine<C>, nextState: string)
		if nextState == self.current then
			return
		end
		local target = states[nextState]
		assert(target, `Unknown state {nextState}`)
		local old = states[self.current]
		if old.exit then
			old.exit(self.context)
		end
		self.current = nextState
		if target.enter then
			target.enter(self.context)
		end
	end

	function machine.step(self: Machine<C>, dt: number)
		local state = states[self.current]
		if state.update then
			local nextState = state.update(self.context, dt)
			if nextState then
				self:transition(nextState)
			end
		end
	end

	local first = states[initial]
	if first.enter then
		first.enter(context)
	end
	return machine
end

return StateMachine
```
<!-- /code -->

### Guard brain (idle, chase, return)

<!-- code: ServerScriptService/Services/GuardNpc.luau -->
```lua
--!strict
-- ServerScriptService/Services/GuardNpc (ModuleScript, server only)
-- Guard NPC brain: Idle at post -> Chase nearest target in range -> Return to post.
-- Uses the shared StateMachine, MoveTo for short direct chases and
-- PathfindingService for the trip home. Ticks at 5 Hz, not every frame.
local PathfindingService = game:GetService("PathfindingService")
local Players = game:GetService("Players")
local ServerScriptService = game:GetService("ServerScriptService")

local StateMachine = require(ServerScriptService.Shared.StateMachine)

local TICK = 0.2 -- seconds between brain updates
local AGGRO_RANGE = 40
local LEASH_RANGE = 90 -- give up when this far from the post

type Context = {
	model: Model,
	humanoid: Humanoid,
	root: BasePart,
	post: Vector3,
	target: Player?,
	path: Path,
	returning: boolean,
}

local function findTarget(context: Context): Player?
	local best: Player? = nil
	local bestDistance = AGGRO_RANGE
	for _, player in Players:GetPlayers() do
		local character = player.Character
		local root = character and character:FindFirstChild("HumanoidRootPart")
		local humanoid = character and character:FindFirstChildOfClass("Humanoid")
		if root and root:IsA("BasePart") and humanoid and humanoid.Health > 0 then
			local distance = (root.Position - context.root.Position).Magnitude
			if distance < bestDistance then
				best, bestDistance = player, distance
			end
		end
	end
	return best
end

local function targetPosition(player: Player): Vector3?
	local character = player.Character
	local root = character and character:FindFirstChild("HumanoidRootPart")
	return if root and root:IsA("BasePart") then root.Position else nil
end

local states: { [string]: StateMachine.State<Context> } = {
	Idle = {
		update = function(context)
			context.target = findTarget(context)
			return if context.target then "Chase" else nil
		end,
	},
	Chase = {
		update = function(context)
			local target = context.target
			local position = target and targetPosition(target)
			if not position or (context.root.Position - context.post).Magnitude > LEASH_RANGE then
				return "Return"
			end
			-- Short, mostly open chases: direct MoveTo, refreshed every tick.
			context.humanoid:MoveTo(position)
			return nil
		end,
	},
	Return = {
		enter = function(context)
			context.target = nil
			context.returning = true
			-- Path home asynchronously so the brain loop never blocks on ComputeAsync.
			task.spawn(function()
				local ok = pcall(function()
					context.path:ComputeAsync(context.root.Position, context.post)
				end)
				if not ok or context.path.Status ~= Enum.PathStatus.Success then
					context.humanoid:MoveTo(context.post) -- fall back to a straight walk
					return
				end
				for _, waypoint in context.path:GetWaypoints() do
					if not context.returning then
						return -- re-aggroed or despawned mid-path
					end
					if waypoint.Action == Enum.PathWaypointAction.Jump then
						context.humanoid.Jump = true
					end
					context.humanoid:MoveTo(waypoint.Position)
					context.humanoid.MoveToFinished:Wait()
				end
			end)
		end,
		update = function(context)
			if (context.root.Position - context.post).Magnitude < 4 then
				return "Idle"
			end
			context.target = findTarget(context)
			return if context.target then "Chase" else nil
		end,
		exit = function(context)
			context.returning = false
		end,
	},
}

local GuardNpc = {}

-- Starts the brain; returns a function that stops it.
function GuardNpc.start(model: Model): () -> ()
	local humanoid = model:FindFirstChildOfClass("Humanoid")
	local root = model.PrimaryPart
	assert(humanoid and root, `{model:GetFullName()} needs a Humanoid and PrimaryPart`)
	-- The server simulates guards: clients must not own their physics.
	root:SetNetworkOwner(nil)
	-- Guards never climb or swim: disabling unused states saves CPU.
	humanoid:SetStateEnabled(Enum.HumanoidStateType.Climbing, false)
	humanoid:SetStateEnabled(Enum.HumanoidStateType.Swimming, false)

	local context: Context = {
		model = model,
		humanoid = humanoid,
		root = root,
		post = root.Position,
		target = nil,
		path = PathfindingService:CreatePath({ AgentRadius = 2, AgentHeight = 5, AgentCanJump = true }),
		returning = false,
	}
	local machine = StateMachine.new(states, "Idle", context)

	local running = true
	task.spawn(function()
		while running and model.Parent and humanoid.Health > 0 do
			machine:step(TICK)
			task.wait(TICK)
		end
	end)
	return function()
		running = false
		context.returning = false
	end
end

return GuardNpc
```
<!-- /code -->

## Scaling to many NPCs

From the official performance guidance:
- **Disable unused `HumanoidStateType`s** (Climbing, Swimming and so on) with `SetStateEnabled`.
- **No Humanoid for static NPCs.** Use an `AnimationController` + `Animator`. For moving NPCs, a custom mover (AlignPosition, LinearVelocity, or CFrame on anchored models) plus an AnimationController can replace Humanoids.
- **Animate NPCs on clients**, only near the player, to cut server load and replication.
- **Pool NPC models** instead of destroy and respawn. **Spawn only near players**, and cull them when players leave.
- **Avoid changing avatar hierarchies and sizes after spawn** (FastCluster rebuilds).
- **Parallel Luau:** put per-NPC brains under `Actor`s for heavy logic. Decisions run in parallel; instance writes happen serially.
- **Tick rates:** stagger brains (random start offsets) so they don't all think on the same frame.

## Client vs server responsibilities

| Server | Client |
|---|---|
| Spawning, decisions, pathing, physics ownership, damage, loot | Animation (for crowds), VFX, sounds, nameplates, health bars, interpolation of cosmetic motion |

## Common mistakes

- Running the brain on Heartbeat for every NPC.
- `ComputeAsync` every frame while chasing.
- Leaving NPC network ownership on automatic. A nearby client can then teleport or fling it.
- Full Humanoids on dozens of decorative NPCs.
- No leash or give-up rule, so NPCs chase forever across the map.
- Ignoring `path.Blocked`, so NPCs walk into walls after a door closes.

## Related notes

- [[Combat Systems Engineering]]
- [[Performance and Profiling Guide]]
- [[Roblox Animation Engineering]]
- [[Network Ownership and Physics Security]]

## Sources

AUTHORITATIVE:
- [Pathfinding (limits, modifiers, links, streaming)](https://create.roblox.com/docs/characters/pathfinding)
- [Improve performance: Humanoids](https://create.roblox.com/docs/performance-optimization/improve)
- [Network ownership](https://create.roblox.com/docs/physics/network-ownership)
- [Parallel Luau](https://create.roblox.com/docs/scripting/multithreading)

Behaviour-architecture patterns: standard game-AI practice. USEFUL.

## Last Verified

2026-10-14
