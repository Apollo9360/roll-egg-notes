---
title: Recipe - Secure Remote Handler
category: Recipes
tags: [recipe, security, remotes, rate-limit, validation, luau]
status: active
source_quality: AUTHORITATIVE pattern; code type-checked with luau-lsp and Roblox definitions
last_verified: 2026-10-14
aliases: [Secure RemoteEvent, Remote rate limiter, Token bucket, Validate module]
---

# Recipe - Secure Remote Handler

## Summary

Three small modules that every remote handler can reuse:
- `Validate`: type and value checks that are safe against NaN, inf and spoofed tables.
- `RateLimiter`: a per-player token bucket.
- `ShopService`: a full secure purchase handler showing the check order.

All three were type-checked in `--!strict` mode with luau-lsp 1.70.1 and Roblox API definitions (2026-10-14).

## When to use it

Use it for any remote, ProximityPrompt, ClickDetector or other client-triggered server logic.

For purchases with **Robux**, use [[Developer Product Receipt Pipeline]] instead. This recipe covers in-game currency.

## The check order

1. **Rate limit**: cheapest check, stops spam before work.
2. **Types**: `typeof`, which also catches tables posing as Instances.
3. **Values**: `math.isfinite`, ranges, integers, allow-lists.
4. **Context**: server-side position, ownership, cooldown, alive.
5. **Mutate atomically**: no yield between check and write.
6. **Reply or replicate.**

## Validate (ReplicatedStorage/Shared/Validate)

Pure functions, so it is safe to replicate. Clients can reuse it for UX pre-checks; the server still re-checks.

<!-- code: ReplicatedStorage/Shared/Validate.luau -->
```lua
--!strict
-- ReplicatedStorage/Shared/Validate (ModuleScript)
-- Small, composable checks for remote arguments. Every check returns a boolean
-- so handlers can reject early with a single `if not ... then return end`.
local Validate = {}

-- Finite number (rejects NaN, +inf, -inf) inside [min, max].
function Validate.number(value: unknown, min: number, max: number): boolean
	if typeof(value) ~= "number" then
		return false
	end
	local n = value :: number
	-- math.isfinite rejects NaN and +/-inf; NaN fails every comparison, so
	-- range checks alone would let it through.
	return math.isfinite(n) and n >= min and n <= max
end

-- Whole number inside [min, max]; use for quantities, slots and indexes.
function Validate.integer(value: unknown, min: number, max: number): boolean
	return Validate.number(value, min, max) and (value :: number) % 1 == 0
end

-- Valid UTF-8 string with a length cap (DataStores reject invalid UTF-8).
function Validate.text(value: unknown, maxLength: number): boolean
	if typeof(value) ~= "string" then
		return false
	end
	local length = utf8.len(value :: string)
	return length ~= nil and length <= maxLength
end

-- Vector3 with all components finite.
function Validate.vector3(value: unknown): boolean
	if typeof(value) ~= "Vector3" then
		return false
	end
	local v = value :: Vector3
	return math.isfinite(v.X) and math.isfinite(v.Y) and math.isfinite(v.Z)
end

-- A real Instance (not a spoofed table) of the given class under a known parent.
function Validate.instanceIn(value: unknown, className: string, ancestor: Instance): boolean
	if typeof(value) ~= "Instance" then
		return false
	end
	local inst = value :: Instance
	return inst:IsA(className) and inst:IsDescendantOf(ancestor)
end

-- A key from an allow-list table, e.g. Validate.oneOf(itemId, ItemCatalog).
function Validate.oneOf(value: unknown, allowed: { [string]: any }): boolean
	return typeof(value) == "string" and allowed[value :: string] ~= nil
end

return Validate
```
<!-- /code -->

## RateLimiter (ServerScriptService/Shared/RateLimiter)

Token bucket, as recommended in the official security docs:
- `capacity` is the burst size.
- `windowSeconds` is the time to refill a full bucket.

This version keys by `Player` and clears buckets on `PlayerRemoving`, so it can't leak memory.

<!-- code: ServerScriptService/Shared/RateLimiter.luau -->
```lua
--!strict
-- ServerScriptService/Shared/RateLimiter (ModuleScript, server only)
-- Token bucket per player: allows short bursts, enforces an average rate.
local Players = game:GetService("Players")

export type RateLimiter = {
	allow: (self: RateLimiter, player: Player) -> boolean,
	destroy: (self: RateLimiter) -> (),
}

type Bucket = { tokens: number, last: number }

local RateLimiter = {}

-- capacity: burst size; windowSeconds: time to refill a full bucket.
function RateLimiter.new(capacity: number, windowSeconds: number): RateLimiter
	assert(capacity >= 1, "capacity must be >= 1")
	assert(windowSeconds > 0, "windowSeconds must be > 0")
	local refillPerSecond = capacity / windowSeconds
	local buckets: { [Player]: Bucket } = {}

	-- Forget players who leave so the table can't grow forever.
	local removingConnection = Players.PlayerRemoving:Connect(function(player)
		buckets[player] = nil
	end)

	local limiter = {} :: RateLimiter

	function limiter.allow(_self: RateLimiter, player: Player): boolean
		local now = os.clock()
		local bucket = buckets[player]
		if bucket == nil then
			bucket = { tokens = capacity, last = now }
			buckets[player] = bucket
		else
			local elapsed = now - bucket.last
			bucket.tokens = math.min(capacity, bucket.tokens + elapsed * refillPerSecond)
			bucket.last = now
		end
		if bucket.tokens >= 1 then
			bucket.tokens -= 1
			return true
		end
		return false
	end

	function limiter.destroy(_self: RateLimiter)
		removingConnection:Disconnect()
		table.clear(buckets)
	end

	return limiter
end

return RateLimiter
```
<!-- /code -->

Typical settings. These are judgment calls, not official numbers; tune them to your game:

| Action | capacity, window |
|---|---|
| Chat or emote | 5, 10 s |
| Shop purchase | 5, 5 s |
| Ability use | Equal to the server cooldown; enforce the cooldown separately |
| UI state query | 10, 2 s |

## ShopService (ServerScriptService/Services/ShopService)

<!-- code: ServerScriptService/Services/ShopService.luau -->
```lua
--!strict
-- ServerScriptService/Services/ShopService (ModuleScript, server only)
-- Secure purchase flow: the client only names WHAT it wants; the server owns
-- prices, balances, distance rules and the rate limit.
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local ServerScriptService = game:GetService("ServerScriptService")

local Validate = require(ReplicatedStorage.Shared.Validate)
local RateLimiter = require(ServerScriptService.Shared.RateLimiter)

type CatalogEntry = { price: number, maxQuantity: number }

-- Server-only catalog. Never trust a price sent by the client.
local CATALOG: { [string]: CatalogEntry } = {
	SpeedCoil = { price = 250, maxQuantity = 1 },
	Potion = { price = 40, maxQuantity = 10 },
}
local MAX_SHOP_DISTANCE = 20

-- Stand-in for your player-data module (see Production Data Persistence).
local coins: { [Player]: number } = {}
local inventory: { [Player]: { [string]: number } } = {}

local ShopService = {}

local function isNearShop(player: Player, shopPart: BasePart): boolean
	local character = player.Character
	local root = character and character:FindFirstChild("HumanoidRootPart")
	if not root or not root:IsA("BasePart") then
		return false
	end
	return (root.Position - shopPart.Position).Magnitude <= MAX_SHOP_DISTANCE
end

function ShopService.init(remote: RemoteFunction, shopPart: BasePart)
	local limiter = RateLimiter.new(5, 5) -- bursts of 5, about 1 purchase/second sustained

	remote.OnServerInvoke = function(player: Player, itemId: unknown, quantity: unknown)
		-- 1. Rate limit first: it is the cheapest check.
		if not limiter:allow(player) then
			return false, "TooFast"
		end
		-- 2. Type and value validation against server-side data.
		if not Validate.oneOf(itemId, CATALOG) then
			return false, "UnknownItem"
		end
		local entry = CATALOG[itemId :: string]
		if not Validate.integer(quantity, 1, entry.maxQuantity) then
			return false, "BadQuantity"
		end
		-- 3. Context validation: state the server already knows.
		if not isNearShop(player, shopPart) then
			return false, "TooFar"
		end
		local total = entry.price * (quantity :: number)
		local balance = coins[player] or 0
		if balance < total then
			return false, "NotEnoughCoins"
		end
		-- 4. Mutate authoritative state in one step, with no yield between check and write.
		coins[player] = balance - total
		local items = inventory[player] or {}
		items[itemId :: string] = (items[itemId :: string] or 0) + (quantity :: number)
		inventory[player] = items
		return true, coins[player]
	end

	Players.PlayerRemoving:Connect(function(player)
		coins[player] = nil
		inventory[player] = nil
	end)
end

return ShopService
```
<!-- /code -->

### Client side (sketch)

```lua
-- LocalScript (StarterPlayerScripts). UX only: the server decides.
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local buyItem = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("BuyItem") :: RemoteFunction

local function onBuyClicked(itemId: string)
	local ok, result = buyItem:InvokeServer(itemId, 1)
	if ok then
		-- result = new balance; play purchase feedback
	else
		-- result = reason code, e.g. "NotEnoughCoins"; show a friendly message
	end
end
```

## Adapting it

- **Swap the stand-in state.** Replace the `coins` and `inventory` tables with your player-data module. Keep the "check, then write, no yield" rule even then.
- **Return reason codes**, not human text. The client maps codes to localized messages.
- **Log rejections.** Count `TooFast`, `BadQuantity` and `UnknownItem` per player and feed the counts into a suspicion score. Malformed requests never come from your real client. See [[Remote Security and Anti-Exploit]].
- **Prompts.** For a ProximityPrompt, use the same pattern inside `Triggered`. Also check `prompt.Enabled` and the player's server-side distance.

## Common mistakes

- Putting `RateLimiter` in ReplicatedStorage. That's harmless, but server modules belong in ServerScriptService.
- Calling `task.wait`, `DataStore`, `WaitForChild` or any other yielding call between the balance check and the deduction.
- Trusting `quantity` without an integer check. `0.5` or `-1` can mint currency.

## Related notes

- [[Roblox Networking Best Practices]]
- [[Remote Security and Anti-Exploit]]
- [[Remote Security Checklist]]
- [[Data Persistence Guide]]

## Sources

- [Securing the client-server boundary (token bucket, NaN, shop flow)](https://create.roblox.com/docs/scripting/security/client-server-boundary): AUTHORITATIVE.
- [math library: `math.isfinite`](https://create.roblox.com/docs/reference/engine/libraries/math): AUTHORITATIVE.

## Last Verified

2026-10-14
