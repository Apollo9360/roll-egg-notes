---
title: Cleanup Pattern (Maid Janitor Trove)
category: Code Patterns
tags: [pattern, cleanup, memory-leaks, connections, luau]
status: active
source_quality: HIGH CONFIDENCE pattern; code type-checked with luau-lsp
last_verified: 2026-10-14
aliases: [Maid, Janitor, Trove, Connection cleanup, Disposable]
---

# Cleanup Pattern (Maid Janitor Trove)

## Summary

Every feature that **creates** something (connections, instances, threads, tweens) registers it with one cleanup object. When the feature ends (a player leaves, a character dies, a UI closes, a tagged instance is removed), call `clean()` once.

This is the single most effective defence against connection and memory leaks in Roblox.

## When to use it

Use it for anything with a lifetime shorter than the server or client session:
- per-player state;
- per-character effects;
- per-round systems;
- UI screens;
- tagged objects, which stream in and out;
- abilities and projectiles.

## When not to use it

- Static, session-long connections in a service's `start()`, such as `Players.PlayerAdded`. They never need cleaning.
- Hot paths creating thousands of short-lived objects per second. Use pooling instead ([[Performance and Profiling Guide]]).

## Code

<!-- code: ReplicatedStorage/Shared/Cleanup.luau -->
```lua
--!strict
-- ReplicatedStorage/Shared/Cleanup (ModuleScript)
-- Minimal "Maid/Janitor/Trove": collect everything a feature creates, then free it
-- all with one call. Prevents connection, instance and thread leaks.
-- For a fuller version, use Trove (sleitnick/RbxUtil) or Janitor.
export type Task = RBXScriptConnection | Instance | thread | () -> ()

export type Cleanup = {
	-- item must be a Task (connection, Instance, thread or function); returns it unchanged.
	add: <T>(self: Cleanup, item: T) -> T,
	connect: (self: Cleanup, signal: RBXScriptSignal, handler: (...any) -> ()) -> RBXScriptConnection,
	clean: (self: Cleanup) -> (),
}

local function release(item: Task)
	if typeof(item) == "RBXScriptConnection" then
		item:Disconnect()
	elseif typeof(item) == "Instance" then
		item:Destroy()
	elseif typeof(item) == "thread" then
		-- A thread can't cancel itself while it is the one running clean().
		if coroutine.running() ~= item then
			task.cancel(item)
		end
	elseif typeof(item) == "function" then
		item()
	end
end

local Cleanup = {}

function Cleanup.new(): Cleanup
	local items: { Task } = {}
	local self = {} :: Cleanup

	function self.add<T>(_self: Cleanup, item: T): T
		local kind = typeof(item)
		assert(
			kind == "RBXScriptConnection" or kind == "Instance" or kind == "thread" or kind == "function",
			`Cleanup:add got unsupported {kind}`
		)
		table.insert(items, item :: any)
		return item
	end

	function self.connect(_self: Cleanup, signal: RBXScriptSignal, handler: (...any) -> ()): RBXScriptConnection
		local connection = signal:Connect(handler)
		table.insert(items, connection)
		return connection
	end

	-- Releases in reverse order (last created, first destroyed). Safe to call twice.
	function self.clean(_self: Cleanup)
		while #items > 0 do
			release(table.remove(items) :: Task)
		end
	end

	return self
end

return Cleanup
```
<!-- /code -->

### Usage: per character

```lua
local Cleanup = require(ReplicatedStorage.Shared.Cleanup)

local function onCharacterAdded(character: Model)
	local cleanup = Cleanup.new()
	local humanoid = character:WaitForChild("Humanoid") :: Humanoid

	cleanup:connect(humanoid.HealthChanged, function(health) updateHealthBar(health) end)
	cleanup:add(createAuraEffect(character))          -- an Instance
	cleanup:add(task.spawn(regenLoop, humanoid))       -- a thread
	cleanup:add(function() hideHealthBar() end)        -- custom teardown

	-- One teardown for everything above:
	cleanup:connect(character.Destroying, function() cleanup:clean() end)
end
```

### Usage: tagged objects with streaming

```lua
local cleanups: { [Instance]: Cleanup.Cleanup } = {}
local function setUp(door: Instance)
	local cleanup = Cleanup.new()
	cleanups[door] = cleanup
	-- ... connect prompts, tweens, sounds via cleanup
end
local function tearDown(door: Instance)
	local cleanup = cleanups[door]
	if cleanup then cleanup:clean(); cleanups[door] = nil end
end
CollectionService:GetInstanceAddedSignal("Door"):Connect(setUp)
CollectionService:GetInstanceRemovedSignal("Door"):Connect(tearDown)
```

## Principles

- **Whoever creates it owns its cleanup.** Pass a cleanup object into helpers instead of having them return loose connections.
- **Clean in reverse creation order.** Dependents are destroyed before what they depend on.
- **Idempotent.** Calling `clean()` twice is safe.
- **Per-player tables** keyed by `Player` must also be cleared in `PlayerRemoving`. Cleanup objects don't help if the table still holds the key.
- **`Instance:Destroy()` disconnects that instance's own events.** Connections to *other* objects' events, made in its name, stay alive. That is the classic leak this pattern fixes.

## Library options

- **Trove** ([RbxUtil](https://github.com/Sleitnick/RbxUtil)): `trove:Add`, `trove:Connect`, `trove:Clone`, `trove:Construct`, `trove:AttachToInstance`, `trove:Clean`.
- **Janitor** ([howmanysmall/Janitor](https://github.com/howmanysmall/Janitor)): named indices, promises, `LinkToInstance`.
- The module above has no dependencies; swap in either library when you need more.

## Common mistakes

- Connecting inside `CharacterAdded` without disconnecting on death or respawn. Handlers pile up every life.
- `while true do ... task.wait() end` loops that never check whether their object still exists. Register the thread and cancel it.
- Forgetting `GetInstanceRemovedSignal` when using tags with StreamingEnabled.

## Related notes

- [[Roblox Stability Engineering]]
- [[Roblox Project Architecture]]
- [[Performance and Profiling Guide]]

## Sources

- [Roblox Instance:Destroy and Destroying](https://create.roblox.com/docs/reference/engine/classes/Instance#Destroy). AUTHORITATIVE.
- [Deferred engine events (disconnect semantics)](https://create.roblox.com/docs/scripting/events/deferred). AUTHORITATIVE.
- Trove (RbxUtil, updated 2026-07) and Janitor (updated 2025-10). USEFUL; community-standard implementations of the pattern.

## Last Verified

2026-10-14
