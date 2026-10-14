---
title: Recipe - Player Data with ProfileStore
category: Recipes
tags: [recipe, datastore, profilestore, session-locking, migration, luau]
status: active
source_quality: HIGH CONFIDENCE; code type-checked with luau-lsp against ProfileStore's own types
last_verified: 2026-10-14
aliases: [PlayerData module, Session locking recipe, Save migration recipe, DataStore retry system]
---

# Recipe - Player Data with ProfileStore

## Summary

This is a typed `PlayerData` service using **ProfileStore** (MAD STUDIO, Apache-2.0). ProfileStore handles:
- session locking;
- auto-saving;
- per-key queued retries;
- saving every active profile on shutdown.

This module adds a schema version with step-by-step migration, a load timeout, a single path for changing coins, and replication of the balance through a player attribute.

## Setup

1. Get ProfileStore from [GitHub](https://github.com/MadStudioRoblox/ProfileStore) or the Creator Store, and put the ModuleScript at `ServerScriptService/Packages/ProfileStore`.
2. Save the module below as `ServerScriptService/Services/PlayerData`.
3. From one server bootstrap script, call `PlayerData.start()` **before** anything that reads data. Gameplay code then calls `PlayerData.get(player)` (non-blocking) or `PlayerData.waitFor(player)` (yields up to 30 s).

## Code

<!-- code: ServerScriptService/Services/PlayerData.luau -->
```lua
--!strict
-- ServerScriptService/Services/PlayerData (ModuleScript, server only)
-- Session-locked player profiles on top of ProfileStore (MAD STUDIO, Apache-2.0).
-- One key per player, in-memory reads and writes, auto-save, schema migration.
local Players = game:GetService("Players")
local ServerScriptService = game:GetService("ServerScriptService")

local ProfileStore = require(ServerScriptService.Packages.ProfileStore)

local SCHEMA_VERSION = 2
local LOAD_TIMEOUT = 30 -- seconds a caller will wait for a profile before giving up

export type Data = {
	SchemaVersion: number,
	Coins: number,
	Inventory: { [string]: number },
	PurchaseIds: { string }, -- recent Developer Product receipts (see ReceiptHandler)
}

-- Defaults for new players. Reconcile() copies missing keys from here into old profiles.
local TEMPLATE: Data = {
	SchemaVersion = SCHEMA_VERSION,
	Coins = 0,
	Inventory = {},
	PurchaseIds = {},
}

-- The store name is part of the save location: renaming it starts everyone from scratch.
local store: ProfileStore.ProfileStore<any> = ProfileStore.New("PlayerData", TEMPLATE :: any)
-- Profile<Data> trips Luau's checker on ProfileStore's `T & JSONAcceptable`
-- intersection, so profiles are typed loosely and Data is cast where read.
type Profile = ProfileStore.Profile<any>

local profiles: { [Player]: Profile } = {}

local PlayerData = {}

-- Upgrade old saves one version at a time. Never delete a step once it has shipped.
-- Runs BEFORE Reconcile(): Reconcile would copy the template's current SchemaVersion
-- into old saves that lack the field, and their migration would be skipped.
local function migrate(data: any)
	data.SchemaVersion = data.SchemaVersion or 1 -- saves from before versioning existed
	if data.SchemaVersion < 2 and data.Inventory ~= nil then
		-- v1 stored inventory as an array of item ids; v2 stores counts per id.
		local old = data.Inventory
		local counts: { [string]: number } = {}
		for _, itemId in ipairs(old) do
			counts[itemId] = (counts[itemId] or 0) + 1
		end
		data.Inventory = counts
	end
	data.SchemaVersion = SCHEMA_VERSION
end

local function onPlayerAdded(player: Player)
	-- Cancel stops waiting for the session lock if the player already left.
	-- (ProfileStore's exported type omits Cancel, hence the cast.)
	local params: any = {
		Cancel = function()
			return player.Parent ~= Players
		end,
	}
	local profile = store:StartSessionAsync(`Player_{player.UserId}`, params)
	if profile == nil then
		-- Only expected while the server is shutting down or DataStores are down.
		player:Kick("Your data couldn't be loaded. Please rejoin.")
		return
	end

	profile:AddUserId(player.UserId) -- lets Roblox's right-to-be-forgotten tools find this key
	migrate(profile.Data)
	profile:Reconcile() -- then fill keys added to TEMPLATE since this save was written

	profile.OnSessionEnd:Connect(function()
		profiles[player] = nil
		player:Kick("Your data was opened on another server. Please rejoin.")
	end)

	if player.Parent ~= Players then
		profile:EndSession() -- left while loading
		return
	end
	profiles[player] = profile
	player:SetAttribute("Coins", profile.Data.Coins) -- replicated for the HUD
end

-- Returns the live profile, or nil if it isn't loaded (yet).
function PlayerData.get(player: Player): Profile?
	local profile = profiles[player]
	if profile and profile:IsActive() then
		return profile
	end
	return nil
end

-- Yields until the profile loads, the player leaves, or LOAD_TIMEOUT passes.
function PlayerData.waitFor(player: Player): Profile?
	local deadline = os.clock() + LOAD_TIMEOUT
	while player.Parent == Players and os.clock() < deadline do
		local profile = PlayerData.get(player)
		if profile then
			return profile
		end
		task.wait(0.1)
	end
	return nil
end

-- The only way gameplay code should change coins: keeps the replicated attribute in sync.
function PlayerData.addCoins(player: Player, amount: number): boolean
	local profile = PlayerData.get(player)
	if not profile or not math.isfinite(amount) then
		return false
	end
	local data = profile.Data :: Data
	local newBalance = data.Coins + amount
	if newBalance < 0 then
		return false
	end
	data.Coins = newBalance
	player:SetAttribute("Coins", newBalance)
	return true
end

function PlayerData.start()
	for _, player in Players:GetPlayers() do
		task.spawn(onPlayerAdded, player)
	end
	Players.PlayerAdded:Connect(onPlayerAdded)
	Players.PlayerRemoving:Connect(function(player)
		local profile = profiles[player]
		if profile then
			profile:EndSession() -- final save + releases the session lock
		end
		profiles[player] = nil
	end)
	-- ProfileStore saves and releases all active sessions on server shutdown itself.
end

return PlayerData
```
<!-- /code -->

## Why it is built this way

- **One key per player** (`Player_{UserId}`) holds a single table. Changes are atomic, and the static pattern lets Roblox's right-to-be-forgotten tools find it. `AddUserId` links the key to the user.
- **`Cancel`** stops waiting for a session lock once the player has left, so loads don't pile up.
- **`OnSessionEnd` → kick.** Another server took the session, for example after a fast rejoin. The data here is no longer authoritative, so the player must not keep playing on it.
- **Migrate first, then `Reconcile`.** `Reconcile` copies missing keys from the template, including the *current* `SchemaVersion`. If it ran first, a save written before versioning existed would look up to date and skip its migration. So `migrate` treats a missing version as 1, upgrades step by step, and only then `Reconcile` adds new keys. To change the schema, bump `SCHEMA_VERSION` and add one `if data.SchemaVersion < N` step.
- **`addCoins` is the single write path.** Every balance change keeps the replicated `Coins` attribute in sync, and the function rejects NaN, inf and overdrafts.
- **Typing note.** ProfileStore's exported `Profile<T>` uses `T & JSONAcceptable`, which the current Luau checker cannot unify. That is why profiles are typed `Profile<any>` and `Data` is cast at each read.

## Extending it

| Need | Do |
|---|---|
| Private data the client must not see | Don't mirror it to attributes. Send it through a remote to that player only. |
| Many replicated fields | Add a `changed` signal and a PlayerDataClient that receives deltas through one RemoteEvent. |
| Tests without real DataStores | Use `ProfileStore.New(...).Mock:StartSessionAsync(...)`. |
| Global leaderboard | Not ProfileStore. Write to an `OrderedDataStore` on save, or use a MemoryStore sorted map. |
| Robux purchases | [[Developer Product Receipt Pipeline]] |

## Common mistakes

- Reading `profile.Data` before the load finishes. Call `PlayerData.start()` early, and have systems use `waitFor`.
- Keeping a reference to `profile.Data` after the session ends.
- Storing Instances, Vector3s or other non-JSON values. Convert them to plain tables of numbers and strings.
- Changing the store name (`"PlayerData"`) or the key pattern after launch. Everyone's progress appears wiped.

## Related notes

- [[Data Persistence Guide]]
- [[Production Data Persistence Checklist]]
- [[Developer Product Receipt Pipeline]]

## Sources

- [ProfileStore README and tutorial](https://github.com/MadStudioRoblox/ProfileStore) by loleris (repo state 2025-07-31). HIGH CONFIDENCE, widely used.
- [Implement player data and purchasing systems](https://create.roblox.com/docs/cloud-services/data-stores/player-data-purchasing). AUTHORITATIVE.

## Last Verified

2026-10-14
