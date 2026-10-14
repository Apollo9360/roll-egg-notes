---
title: Recipe - Server Cooldown
category: Recipes
tags: [recipe, cooldown, security, remotes, luau]
status: active
source_quality: Vault principles (server authority, rate limiting); code type-checked
last_verified: 2026-10-14
aliases: [Cooldown, Ability cooldown, Server cooldowns, Debounce]
---

# Recipe - Server Cooldown

## Summary

Cooldowns that matter (abilities, steals, rewards, purchases with game currency) must be enforced **on the server**. The client can show a cooldown ring for feel, but an exploiter can skip any client check. Compare timestamps at request time; no loops or timers are needed ([[Decision - Polling vs Events]]).

A cooldown is not a rate limiter:
- a **rate limiter** caps how often a remote can be *called* at all, against spam ([[Recipe - Secure Remote Handler]]);
- a **cooldown** is a *game rule*: "you can steal once every 2 seconds".

Use both: the rate limiter first, then validation, then the cooldown.

## Code

Type-checked with luau-lsp; not yet run in a live game.

<!-- code: ServerScriptService/Shared/Cooldown.luau -->
```lua
--!strict
-- ServerScriptService/Shared/Cooldown (ModuleScript, server only)
-- Server-side cooldowns per player and action. The client may show its own
-- cooldown for feel, but only this decides whether an action happens.
local Players = game:GetService("Players")

export type Cooldown = {
	-- Starts the cooldown and returns true if the action is ready; returns false otherwise.
	try: (self: Cooldown, player: Player, action: string) -> boolean,
	-- Seconds left (0 when ready). For UI and error messages.
	remaining: (self: Cooldown, player: Player, action: string) -> number,
	reset: (self: Cooldown, player: Player, action: string?) -> (),
	destroy: (self: Cooldown) -> (),
}

local Cooldown = {}

-- durations: seconds per action name, e.g. { Steal = 2, Sell = 0.5 }.
function Cooldown.new(durations: { [string]: number }): Cooldown
	local readyAt: { [Player]: { [string]: number } } = {}

	local removingConnection = Players.PlayerRemoving:Connect(function(player)
		readyAt[player] = nil
	end)

	local cooldown = {} :: Cooldown

	function cooldown.remaining(_self: Cooldown, player: Player, action: string): number
		local actions = readyAt[player]
		local at = actions and actions[action]
		if at == nil then
			return 0
		end
		return math.max(0, at - os.clock())
	end

	function cooldown.try(self: Cooldown, player: Player, action: string): boolean
		local duration = durations[action]
		assert(duration ~= nil, `unknown cooldown action "{action}"`)
		if self:remaining(player, action) > 0 then
			return false
		end
		local actions = readyAt[player]
		if actions == nil then
			actions = {}
			readyAt[player] = actions
		end
		actions[action] = os.clock() + duration
		return true
	end

	function cooldown.reset(_self: Cooldown, player: Player, action: string?)
		local actions = readyAt[player]
		if actions == nil then
			return
		end
		if action then
			actions[action] = nil
		else
			readyAt[player] = nil
		end
	end

	function cooldown.destroy(_self: Cooldown)
		removingConnection:Disconnect()
		table.clear(readyAt)
	end

	return cooldown
end

return Cooldown
```
<!-- /code -->

Usage in a remote handler:

```lua
local cooldowns = Cooldown.new({ Steal = 2, Sell = 0.5 })

-- after rate limiting and validating the request:
if not cooldowns:try(player, "Steal") then
	return -- or tell the client how long is left: cooldowns:remaining(player, "Steal")
end
-- perform the steal
```

## Notes

- `os.clock()` measures server time; it is not affected by the client.
- Start the cooldown only when the action is **accepted**. If validation fails after `try`, call `reset(player, action)` so a rejected request doesn't cost the player their cooldown.
- Cooldowns that must survive rejoining (daily rewards, long timers) belong in player data as **UTC timestamps** (`os.time()`), not in this in-memory module ([[Data Persistence Guide]]).
- Per-player state is removed on `PlayerRemoving`, so the table doesn't grow forever.

## Common mistakes

- A `debounce` variable on the client as the only check.
- One global cooldown shared by all players.
- `task.wait(cooldown)` inside the handler, which holds a thread per request and is easy to abuse.
- Long cooldowns kept in memory, which reset when the player hops servers.

## Related notes

- [[Recipe - Secure Remote Handler]]
- [[Remote Security and Anti-Exploit]]
- [[Combat Systems Engineering]]

## Sources

- Built on [[Remote Security and Anti-Exploit]] and [[Roblox Networking Best Practices]], which cite the official docs.

## Last Verified

2026-10-14
