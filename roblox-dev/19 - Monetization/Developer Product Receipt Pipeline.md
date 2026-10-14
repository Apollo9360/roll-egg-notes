---
title: Developer Product Receipt Pipeline
category: Monetization
tags: [monetization, developer-products, processreceipt, purchases, security, recipe]
status: active
source_quality: AUTHORITATIVE (ProcessReceipt behaviour) + HIGH CONFIDENCE (ProfileStore pattern); code type-checked
last_verified: 2026-10-14
aliases: [ProcessReceipt, Duplicate receipt protection, Purchase validation, Robux purchases]
---

# Developer Product Receipt Pipeline

## Summary

Grant Developer Products only inside `MarketplaceService.ProcessReceipt`, set **once** in one server script. Grant each `PurchaseId` exactly once, record it in the player's saved data, and return `PurchaseGranted` **only after that save is confirmed**.

Never grant from client signals such as `PromptProductPurchaseFinished`; they can be spoofed.

## How ProcessReceipt behaves

Official API reference, 2026-10-13:
- **When it's called:** for every *unresolved* purchase when the purchase completes, when a purchase prompt succeeds, and **when the user joins a server**.
- **What resolves a purchase:** returning `PurchaseGranted` *and* Roblox recording that on the backend. That record can fail, which leaves the purchase unresolved, so it will be offered again.
- **What retries it:** returning `NotProcessedYet`. There is **no timed retry**; the callback runs again only on the user's next purchase or rejoin.
- **Yielding:** the callback has **no timeout** and may yield as long as the server runs.
- **No callback set:** if you never set it, receipts are **auto-acknowledged** and cannot be recovered.
- **Concurrency:**
  - Pending receipts for one user run in **non-deterministic order**.
  - The callback can run on **two servers at once** for the same purchase if the user joins a second server before the first returns. This is why the `PurchaseId` check and session locking matter.
- **Presence:** the user must be in the server for the callback to run. The user does **not** need to be present for the result to be recorded.
- **Receipt fields:** `PurchaseId`, `PlayerId`, `ProductId`, `PlaceIdWherePurchased`, `CurrencySpent`, `CurrencyType` (always Robux).
- **Store tab:** if your callback is wrong, purchases from the experience page's Store tab can't be granted.

## The pipeline

Roblox's official "handled" criteria are: not previously handled, reflected in this session, and saved to the DataStore.

1. **Find the player.** If they're absent, return `NotProcessedYet`; Roblox retries on rejoin.
2. **Wait for their profile.** This is common on join. Use a timeout, and stop if the player leaves.
3. If `PurchaseId` is **not** in the profile's `PurchaseIds`:
   - grant (inside `pcall`);
   - append the `PurchaseId`;
   - trim the list to the last N entries.
4. **Save, and wait until `LastSavedData` contains the `PurchaseId`.** Then return `PurchaseGranted`.
5. If the save can't be confirmed (session ended, DataStore down), return `NotProcessedYet`. The next call finds the `PurchaseId` already recorded and goes straight to step 4, so the player is never granted twice.

Session locking guarantees this server's in-memory data is the newest. That is what makes the `PurchaseId` check in step 3 reliable. See [[Data Persistence Guide]].

## Code

Requires [[Recipe - Player Data with ProfileStore]]. It is ProfileStore's "PurchaseId cache" approach, typed and with bounded waits.

<!-- code: ServerScriptService/Services/ReceiptHandler.luau -->
```lua
--!strict
-- ServerScriptService/Services/ReceiptHandler (ModuleScript, server only)
-- Developer Product purchases: grant exactly once, and only report PurchaseGranted
-- after the grant is confirmed saved. Set ProcessReceipt in ONE server script only.
local MarketplaceService = game:GetService("MarketplaceService")
local Players = game:GetService("Players")
local ServerScriptService = game:GetService("ServerScriptService")

local PlayerData = require(ServerScriptService.Services.PlayerData)

local PURCHASE_ID_CACHE_SIZE = 50 -- remember this many recent receipts per player
local SAVE_CONFIRM_TIMEOUT = 15 -- seconds to wait for one save attempt to land

-- productId -> grant function. A grant must not yield and must error on failure.
local PRODUCTS: { [number]: (player: Player) -> () } = {
	[0000001] = function(player) -- replace with your real product ids
		assert(PlayerData.addCoins(player, 1000), "addCoins failed")
	end,
}

type ReceiptInfo = {
	PurchaseId: string,
	PlayerId: number,
	ProductId: number,
	CurrencySpent: number,
	PlaceIdWherePurchased: number,
}

local function isPurchaseSaved(profile: any, purchaseId: string): boolean
	local saved = profile.LastSavedData.PurchaseIds
	return saved ~= nil and table.find(saved, purchaseId) ~= nil
end

-- Saves and yields until purchaseId shows up in LastSavedData, or the session ends.
local function waitUntilSaved(profile: any, purchaseId: string): boolean
	while profile:IsActive() do
		if isPurchaseSaved(profile, purchaseId) then
			return true
		end
		local saved = false
		local connection = profile.OnAfterSave:Connect(function()
			saved = true
		end)
		profile:Save()
		local deadline = os.clock() + SAVE_CONFIRM_TIMEOUT
		while not saved and os.clock() < deadline and profile:IsActive() do
			task.wait(0.25)
		end
		connection:Disconnect()
	end
	return isPurchaseSaved(profile, purchaseId)
end

local function processReceipt(receiptInfo: ReceiptInfo): Enum.ProductPurchaseDecision
	local player = Players:GetPlayerByUserId(receiptInfo.PlayerId)
	if not player then
		-- Roblox calls ProcessReceipt again when the player rejoins.
		return Enum.ProductPurchaseDecision.NotProcessedYet
	end
	local profile = PlayerData.waitFor(player)
	if not profile then
		return Enum.ProductPurchaseDecision.NotProcessedYet
	end
	local grant = PRODUCTS[receiptInfo.ProductId]
	if not grant then
		warn(`ReceiptHandler: no grant for product {receiptInfo.ProductId}`)
		return Enum.ProductPurchaseDecision.NotProcessedYet
	end

	local purchaseId = tostring(receiptInfo.PurchaseId)
	local purchaseIds: { string } = profile.Data.PurchaseIds
	-- Session locking means this server holds the newest data, so this check is reliable.
	if not table.find(purchaseIds, purchaseId) then
		local ok, err: any = pcall(grant, player)
		if not ok then
			warn(`ReceiptHandler: grant failed for {purchaseId}: {err}`)
			return Enum.ProductPurchaseDecision.NotProcessedYet
		end
		table.insert(purchaseIds, purchaseId)
		while #purchaseIds > PURCHASE_ID_CACHE_SIZE do
			table.remove(purchaseIds, 1)
		end
	end

	if waitUntilSaved(profile, purchaseId) then
		return Enum.ProductPurchaseDecision.PurchaseGranted
	end
	-- Granted in memory but not confirmed saved: Roblox will retry on the next join,
	-- and the PurchaseIds check above stops a double grant.
	return Enum.ProductPurchaseDecision.NotProcessedYet
end

local ReceiptHandler = {}

function ReceiptHandler.start()
	MarketplaceService.ProcessReceipt = processReceipt :: any
end

return ReceiptHandler
```
<!-- /code -->

### Wiring

In the server bootstrap:
1. Call `PlayerData.start()`.
2. Then call `ReceiptHandler.start()`.

Only this module may set `ProcessReceipt`. A second assignment anywhere replaces the first.

## Game Passes are different

- Passes are owned permanently. Check ownership with `MarketplaceService:UserOwnsGamePassAsync(userId, passId)` on join, and cache the result.
- Grant mid-session in `PromptGamePassPurchaseFinished` on the **server**, then re-verify ownership. Don't record passes as consumables.

## UX notes

- Disable buy buttons while the player's data is errored or not loaded. A purchase can't be saved for that profile.
- Show feedback immediately from the client prompt-finished event, but treat it as cosmetic. The real grant arrives through replicated data.
- Price display and regional pricing: see `production/monetization/` in the official docs and [[Monetization Design]].

## Common mistakes

- Granting in a client script, or from `PromptProductPurchaseFinished`.
- Returning `PurchaseGranted` before the save, which loses the purchase if the server crashes.
- Not recording `PurchaseId`, which double-grants when two servers run the callback or Roblox re-offers the receipt.
- Setting `ProcessReceipt` in several scripts.
- A grant function that yields or can fail silently.
- Kicking a player whose data failed to load during a purchase. Return `NotProcessedYet` instead.

## Related notes

- [[Recipe - Player Data with ProfileStore]]
- [[Data Persistence Guide]]
- [[Remote Security and Anti-Exploit]]
- [[Monetization Design]]

## Sources

- [MarketplaceService.ProcessReceipt API](https://create.roblox.com/docs/reference/engine/classes/MarketplaceService#ProcessReceipt). AUTHORITATIVE.
- [Implement player data and purchasing systems: Developer Product processing](https://create.roblox.com/docs/cloud-services/data-stores/player-data-purchasing). AUTHORITATIVE.
- [Developer products](https://create.roblox.com/docs/production/monetization/developer-products). AUTHORITATIVE.
- [ProfileStore dev products guide](https://github.com/MadStudioRoblox/ProfileStore/blob/main/docs/devproducts/index.md). HIGH CONFIDENCE.

## Last Verified

2026-10-14
