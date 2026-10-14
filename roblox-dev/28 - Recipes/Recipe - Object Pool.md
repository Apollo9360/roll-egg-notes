---
title: Recipe - Object Pool
category: Recipes
tags: [recipe, pooling, performance, vfx, projectiles, luau]
status: active
source_quality: AUTHORITATIVE principle (Roblox recommends pooling NPCs); code type-checked
last_verified: 2026-10-14
aliases: [Object pooling, Instance pool, Projectile pool, Effect pool]
---

# Recipe - Object Pool

## Summary

Keep released Instances in a folder **outside Workspace** and hand them out again, instead of calling `Clone()`/`Destroy()` dozens of times per second. Roblox's performance docs recommend pooling for frequently respawned NPCs; the same idea applies to projectiles, hit sparks and floating damage numbers.

## When to use it

- Spawn rates of many per second, sustained (bullets, sparks, damage text).
- Expensive instances (NPCs with Humanoids or skinned meshes) that respawn often.

## When not to use it

- Rare objects. The cost is a few clones per minute; don't bother.
- Objects with complex hidden state (running scripts, attached connections) that is hard to reset completely. A stale-state bug is worse than the clone cost.

## Code

<!-- code: ReplicatedStorage/Shared/ObjectPool.luau -->
```lua
--!strict
-- ReplicatedStorage/Shared/ObjectPool (ModuleScript)
-- Reuses Instances that are created and destroyed many times per second
-- (projectiles, hit sparks, damage numbers). Cheaper than Clone()/Destroy() churn.
export type ObjectPool<T> = {
	acquire: (self: ObjectPool<T>) -> T,
	release: (self: ObjectPool<T>, item: T) -> (),
	destroy: (self: ObjectPool<T>) -> (),
}

local ObjectPool = {}

-- template: the Instance to clone. reset: puts a released item back in a neutral state.
-- maxSize: items beyond this are destroyed on release instead of kept.
function ObjectPool.new<T>(template: T & Instance, reset: (T) -> (), maxSize: number): ObjectPool<T>
	local free: { T } = {}
	local storage = Instance.new("Folder")
	storage.Name = `{template.Name}Pool`

	local pool = {} :: ObjectPool<T>

	function pool.acquire(_self: ObjectPool<T>): T
		local item = table.remove(free)
		if item ~= nil then
			return item
		end
		return template:Clone() :: any
	end

	function pool.release(_self: ObjectPool<T>, item: T)
		local instance = item :: any
		if #free >= maxSize then
			instance:Destroy()
			return
		end
		reset(item)
		-- Parked outside Workspace: not rendered or simulated, and never replicated
		-- because the folder itself is never parented to a replicated container.
		instance.Parent = storage
		table.insert(free, item)
	end

	function pool.destroy(_self: ObjectPool<T>)
		storage:Destroy()
		table.clear(free)
	end

	return pool
end

return ObjectPool
```
<!-- /code -->

### Usage (client hit sparks)

```lua
local sparkPool = ObjectPool.new(ReplicatedStorage.Assets.HitSpark, function(spark: Part)
	(spark:FindFirstChildOfClass("ParticleEmitter") :: ParticleEmitter):Clear()
end, 30)

local function playHitSpark(position: Vector3)
	local spark = sparkPool:acquire()
	spark.CFrame = CFrame.new(position)
	spark.Parent = workspace.Effects
	(spark:FindFirstChildOfClass("ParticleEmitter") :: ParticleEmitter):Emit(12)
	task.delay(1, function()
		sparkPool:release(spark)
	end)
end
```

## Rules

- **Fully reset state in `reset`**: emitters cleared, transparency and size restored, attributes cleared, velocity zeroed, tweens cancelled.
- **Cap the pool** (`maxSize`) so a burst doesn't keep memory high forever.
- **Pool on the side that creates the object.** VFX pools live on clients; server pools are for server-owned objects such as NPCs.
- **Don't pool server-side cosmetic effects at all.** Create VFX on clients ([[Performance and Profiling Guide]]).

## Related notes

- [[Performance and Profiling Guide]]
- [[VFX Production Guide]]
- [[Combat Systems Engineering]]

## Sources

- [Improve performance: Humanoids, pool NPC models](https://create.roblox.com/docs/performance-optimization/improve). AUTHORITATIVE.

## Last Verified

2026-10-14
