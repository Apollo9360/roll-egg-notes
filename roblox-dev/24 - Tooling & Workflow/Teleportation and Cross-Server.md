---
title: Teleportation and Cross-Server
category: Tooling & Workflow
tags: [teleport, teleportservice, reserved-servers, messagingservice, memorystore, matchmaking, cross-server]
status: active
source_quality: AUTHORITATIVE; code type-checked
last_verified: 2026-10-14
aliases: [TeleportService, TeleportAsync, Reserved servers, Secure teleports, Cross-server messaging, Parties]
---

# Teleportation and Cross-Server

## Summary

- **Teleport from the server** with `TeleportService:TeleportAsync(placeId, players, TeleportOptions?)`. The client `Teleport()` is **deprecated**.
- **Teleports fail sometimes**, so retry with backoff and handle `TeleportInitFailed`.
- **Save data before teleporting.** `TeleportData` is **client-visible and unencrypted**, so put nothing secure in it.
- **Lock down subplaces** with **Access Control for Places → Secure within universe only**.
- **Cross-server communication:** MessagingService (best effort) and MemoryStores (shared state, queues).
- **Testing:** TeleportService doesn't work in Studio playtests. Test in a published game.

## Teleport options

From the official docs:
- **Teleport groups:** pass all players in one `TeleportAsync` call. They stay together, matched by the first player's data.
- **Specific public server:** `TeleportOptions.ServerInstanceId`.
- **Reserved server:**
  - `TeleportOptions.ShouldReserveServer = true` creates a new private match;
  - or `ReservedServerAccessCode` to join an existing one.
  - Use these for matches, dungeons and private instances.
- **Custom loading screen:** `TeleportService:SetTeleportGui()` on the client. Scripts inside that GUI don't run.
- **Non-secure data:** `TeleportOptions:SetTeleportData()`. Read it on arrival with `Player:GetJoinData()`. **Secure data:** DataStores or MemoryStores.

## Security settings

| Setting | Effect |
|---|---|
| Access Control for Places: **Fully open** | Join any place from anywhere (invites, friends, deep links) |
| **Limited to same universe** | Only teleports within the game reach non-start places; client and server teleports both allowed |
| **Secure within universe only** | Only **server-initiated** teleports reach non-start places. Use it for progression gates, test places and reserved-server-only places. It prevents content replicating to unauthorized players. |
| Place Direct Access Control | Per-place override |
| Allow Third Party Teleports (Studio Security) | Teleports to games you don't own |

See [[Remote Security and Anti-Exploit]], Confidentiality section.

## Code

<!-- code: ServerScriptService/Services/SafeTeleport.luau -->
```lua
--!strict
-- ServerScriptService/Services/SafeTeleport (ModuleScript, server only)
-- TeleportAsync with retries + backoff and a retry on TeleportInitFailed.
-- Teleports can fail (full server, flood limit, network). Save player data
-- BEFORE teleporting; never put secure data in TeleportData (client-visible).
local TeleportService = game:GetService("TeleportService")

local MAX_ATTEMPTS = 4
local BASE_DELAY = 1 -- seconds; doubled each retry plus jitter

local SafeTeleport = {}

local function attempt(placeId: number, players: { Player }, options: TeleportOptions?): (boolean, any)
	return pcall(function()
		return TeleportService:TeleportAsync(placeId, players, options)
	end)
end

-- Yields. Returns true once Roblox accepted the teleport request.
function SafeTeleport.teleport(placeId: number, players: { Player }, options: TeleportOptions?): boolean
	for attemptNumber = 1, MAX_ATTEMPTS do
		local ok, result = attempt(placeId, players, options)
		if ok then
			return true
		end
		warn(`[SafeTeleport] attempt {attemptNumber} to {placeId} failed: {result}`)
		if attemptNumber < MAX_ATTEMPTS then
			task.wait(BASE_DELAY * 2 ^ (attemptNumber - 1) + math.random())
		end
	end
	return false
end

-- Retry once more when a teleport fails after it started (e.g. target server full).
function SafeTeleport.start()
	TeleportService.TeleportInitFailed:Connect(function(player: Player, result: Enum.TeleportResult, errorMessage: string, placeId: number, options: TeleportOptions?)
		warn(`[SafeTeleport] init failed for {player.UserId}: {result.Name} {errorMessage}`)
		if result == Enum.TeleportResult.Flooded or result == Enum.TeleportResult.Failure then
			task.wait(2 + math.random())
			if player.Parent then
				SafeTeleport.teleport(placeId, { player }, options)
			end
		end
	end)
end

return SafeTeleport
```
<!-- /code -->

## Cross-server patterns

| Need | Tool | Notes |
|---|---|---|
| Global announcement ("X found a legendary!") | MessagingService `PublishAsync` / `SubscribeAsync` | **Best effort, not guaranteed.** 1 KB message limit. Per-server send and subscription limits apply ([[Data Persistence Guide]]). |
| Server browser or listings | MemoryStore hash map or sorted map with short expirations, refreshed periodically | Stagger refreshes; shard hot keys |
| Matchmaking queue | MemoryStore queue → a server reserves a server and teleports the group | Remove processed items; handle failures (requeue) |
| Global live leaderboard | MemoryStore sorted map, plus an OrderedDataStore for persistence | |
| Cross-server trading | MemoryStore-coordinated escrow plus session-locked DataStores | Advanced; design carefully ([[Research Backlog]]) |
| Live config | **Configs** | Read-only in game; about 5-minute rollout |

## Common mistakes

- Client-side `Teleport()`, which is deprecated and exploitable.
- No retry, so players get stranded on a failed teleport.
- Passing currency or inventory in TeleportData.
- Teleporting before the final save is queued or complete. With session locking, the next server waits for the lock ([[Data Persistence Guide]]).
- Expecting teleports to work in Studio.

## Related notes

- [[Data Persistence Guide]]
- [[Remote Security and Anti-Exploit]]
- [[Roblox Stability Engineering]]

## Sources

- [Teleport between places](https://create.roblox.com/docs/projects/teleport), [Cross-server messaging](https://create.roblox.com/docs/cloud-services/cross-server-messaging), [Memory stores](https://create.roblox.com/docs/cloud-services/memory-stores), [TeleportService API](https://create.roblox.com/docs/reference/engine/classes/TeleportService). AUTHORITATIVE.

## Last Verified

2026-10-14
