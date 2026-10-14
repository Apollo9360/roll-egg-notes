---
title: Decision - CollectionService Tags vs Folders
category: Decision Guides
tags: [decision, collectionservice, tags, architecture, streaming]
status: active
source_quality: AUTHORITATIVE (CollectionService API reference, script locations doc, streaming techniques doc); code type-checked
last_verified: 2026-10-14
aliases: [Tags vs folders, CollectionService vs folders, Tag binder, Tagged instances]
---

# Decision - CollectionService Tags vs Folders

## Summary

- **Use tags for behaviour:** "every instance like this should act like this", wherever it sits. One ModuleScript binds the behaviour to the tag. This is Roblox's own recommendation instead of putting copies of a script inside Workspace objects.
- **Use folders for organisation and ownership:** "these things belong together", "this is player X's plot", "this is the Meadow zone". Folders give you a place in the hierarchy; tags don't.
- Most games use **both**: folders for structure, tags for behaviour.

## Compare

| Question | Tags (CollectionService) | Folders |
|---|---|---|
| Find "all doors in the game" | `GetTagged("Door")`, wherever they are | Only if every door lives in one folder |
| An instance belongs to two groups | Yes, an instance can have many tags | No, it has one parent |
| React when one appears or disappears | `GetInstanceAddedSignal` / `GetInstanceRemovedSignal`, which also fire when a tagged instance enters or leaves the DataModel (including streaming) | `ChildAdded` / `ChildRemoved` on that folder only |
| Ownership and scoping (player plots, zones) | Awkward; needs an attribute or lookups | Natural: the folder is the scope |
| Visible to designers in Studio | Tags section in Properties, Tag Editor | Explorer tree |
| Replication | Server tags replicate to clients and save with the place | The hierarchy replicates as usual |

## Rules from the API reference

- **All tags on an instance replicate together.** If the server adds or removes any tag on an instance, the client's locally added tags on that instance are dropped. Don't mix client-only and server tags on the same instance.
- With **StreamingEnabled**, a streamed-out instance fires the removed signal and a returning one fires the added signal again, and its tags and properties re-sync from the server. Bind with both signals and keep setup idempotent ([[StreamingEnabled and Large Worlds]]).
- `GetTagged` only returns instances inside the DataModel, in no guaranteed order.
- `GetTagged` plus the added signal together catch present **and** future instances. Use both.
- Clean up per-instance connections and tables when the tag is removed or the instance is destroyed, or they leak.
- `TagAdded` / `TagRemoved` fire once per tag's lifetime in the place, not per instance; they are rarely what you want.

## Code: TagBinder

Binds a setup function to a tag, runs it for existing and future instances, and runs each instance's cleanup when it loses the tag or leaves the DataModel. Type-checked with luau-lsp; not yet run in a live game.

<!-- code: ReplicatedStorage/Shared/TagBinder.luau -->
```lua
--!strict
-- ReplicatedStorage/Shared/TagBinder (ModuleScript, server or client)
-- Runs setup for every instance that has a CollectionService tag, now and later,
-- and runs that instance's cleanup when the tag is removed or the instance
-- leaves the DataModel (destroyed, reparented to nil, or streamed out).
local CollectionService = game:GetService("CollectionService")

export type Cleanup = () -> ()

local TagBinder = {}

-- setup may return a cleanup function. Returns a function that unbinds the tag
-- and cleans up every bound instance.
function TagBinder.bind(tag: string, setup: (instance: Instance) -> Cleanup?): () -> ()
	local bound: { [Instance]: true } = {}
	local cleanups: { [Instance]: Cleanup } = {}

	local function add(instance: Instance)
		if bound[instance] then
			return -- already bound (GetTagged and the added signal can overlap)
		end
		bound[instance] = true -- mark before setup so a re-entrant add is ignored
		local ok, result = pcall(setup, instance)
		if not ok then
			warn(`[TagBinder] setup for "{tag}" failed on {instance:GetFullName()}: {result}`)
			return
		end
		if bound[instance] and result then
			cleanups[instance] = result
		elseif result then
			result() -- removed while setup was running: clean up at once
		end
	end

	local function remove(instance: Instance)
		bound[instance] = nil
		local cleanup = cleanups[instance]
		cleanups[instance] = nil
		if cleanup then
			local ok, err: any = pcall(cleanup)
			if not ok then
				warn(`[TagBinder] cleanup for "{tag}" failed: {err}`)
			end
		end
	end

	-- Connect first, then sweep existing instances, so nothing is missed in between.
	local addedConnection = CollectionService:GetInstanceAddedSignal(tag):Connect(add)
	local removedConnection = CollectionService:GetInstanceRemovedSignal(tag):Connect(remove)
	for _, instance in CollectionService:GetTagged(tag) do
		add(instance)
	end

	return function()
		addedConnection:Disconnect()
		removedConnection:Disconnect()
		local instances = {}
		for instance in bound do
			table.insert(instances, instance)
		end
		for _, instance in instances do
			remove(instance)
		end
	end
end

return TagBinder
```
<!-- /code -->

Usage:

```lua
local unbind = TagBinder.bind("SpinningCoin", function(coin)
	local connection = RunService.Heartbeat:Connect(function(dt)
		-- spin the coin
	end)
	return function()
		connection:Disconnect()
	end
end)
```

## Common mistakes

- Only calling `GetTagged` at startup, so instances added later (or streamed in) never get their behaviour.
- Connecting signals per instance and never disconnecting them.
- Adding a tag on the client to an instance whose tags the server also changes.
- A script copied into 200 coins instead of one tag binder.
- Using tag names as data (`"Price_50"`). Put data in attributes ([[Decision - Attributes vs ValueObjects vs Tables]]).

## Related notes

- [[Roblox Project Architecture]]
- [[Decision - Polling vs Events]]
- [[Cleanup Pattern (Maid Janitor Trove)]]
- [[StreamingEnabled and Large Worlds]]

## Sources

- [CollectionService API reference](https://create.roblox.com/docs/reference/engine/classes/CollectionService) (replication, GetTagged, signals, cleanup warning, TagAdded). AUTHORITATIVE.
- [Script locations](https://create.roblox.com/docs/scripting/locations) (tag instances instead of scripts inside Workspace). AUTHORITATIVE.
- [Streaming techniques](https://create.roblox.com/docs/workspace/streaming/techniques) (tagged objects and streaming). AUTHORITATIVE.

## Last Verified

2026-10-14
