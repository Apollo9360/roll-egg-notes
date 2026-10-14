---
title: Analytics and LiveOps Guide
category: Analytics & LiveOps
tags: [analytics, analyticsservice, funnels, economy-events, custom-events, retention, liveops, experiments, configs]
status: active
source_quality: AUTHORITATIVE (AnalyticsService docs) + COMMUNITY (Hud's Guide, linked); code type-checked
last_verified: 2026-10-14
aliases: [AnalyticsService, Funnel events, Onboarding funnel, Economy events, Custom events, Telemetry, LiveOps]
---

# Analytics and LiveOps Guide

## Summary

`AnalyticsService` has three event families:
- **funnel** (onboarding, progression, shop);
- **economy** (sources, sinks, wallet balances);
- **custom** (counters and values).

All three accept up to **3 custom fields** for breakdowns. Events are sent **from the server only** and count **only in published games** (not Studio).

- **Start with an onboarding funnel.** That's where most players are lost.
- **Tune live** with **Configs**, and test with **Experiments**.
- **Interpreting the dashboards** (benchmarks, lag, sample size, what CTR/PTR/QPTR mean) is covered in **Hud's Guide → [[Analytics and Metrics]]**.

## API essentials

From the official docs:

| Family | Method | Rules |
|---|---|---|
| Onboarding funnel | `LogOnboardingFunnelStepEvent(player, step, stepName, customFields?)` | One-time per user. Steps are numbered; repeats are ignored (but still count toward the rate limit); a skipped step auto-completes earlier steps. |
| Other funnels | `LogFunnelStepEvent(player, funnelName, funnelSessionId?, step, stepName, customFields?)` | Recurring funnels such as a shop use a `funnelSessionId`; only the 10 most recent session IDs per user per funnel are tracked |
| Economy | `LogEconomyEvent(player, flowType, currency, amount, endingBalance, transactionType, itemSku?, customFields?)` | `amount` is always positive; flow is `Source` or `Sink`; default transaction types: IAP, TimedReward, Onboarding, Shop, Gameplay, ContextualPurchase |
| Custom | `LogCustomEvent(player, eventName, value?, customFields?)` | Counters (value 1) or values; aggregated daily (up to 24 h delay) |

### Limits

Official; they reset daily:
- Global rate: **120 + 20 × CCU requests per minute**.
- **3 custom fields.** Values beyond 8,000 combined are grouped as "Other".
- Economy: **10 resource types**. Beyond 20 transaction types, or 100 item SKUs, values are grouped as "Other".
- Funnels: **10 funnels**, **100 steps** each. A user's funnel step expires after 180 days without events.
- Custom: **100 event names**. Prefer **one event plus a custom field** over many event names (e.g. `PlantSeed` with field `Plant - Turnip`).

## Wrapper module

<!-- code: ServerScriptService/Services/Analytics.luau -->
```lua
--!strict
-- ServerScriptService/Services/Analytics (ModuleScript, server only)
-- Thin wrapper over AnalyticsService: one place for event names, onboarding steps
-- logged once per player, and no calls in Studio (events only count in published games).
local AnalyticsService = game:GetService("AnalyticsService")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local ENABLED = not RunService:IsStudio()

-- Onboarding funnel: sequential steps from 1. Keep this list stable once shipped;
-- add new steps at the end so dashboard history stays comparable.
local ONBOARDING_STEPS = {
	"Joined",
	"LeftSpawn",
	"ReachedFirstGoal",
	"CompletedFirstLoop",
	"FirstUpgrade",
}

local loggedSteps: { [Player]: { [number]: boolean } } = {}

local Analytics = {}

-- Log onboarding step by name; repeats and returning-player calls are ignored.
function Analytics.onboarding(player: Player, stepName: string)
	local index = table.find(ONBOARDING_STEPS, stepName)
	assert(index, `Unknown onboarding step {stepName}`)
	local steps = loggedSteps[player]
	if not steps or steps[index] then
		return
	end
	steps[index] = true
	if ENABLED then
		AnalyticsService:LogOnboardingFunnelStepEvent(player, index, stepName)
	end
end

-- Economy: amount is always positive; flow says source or sink.
function Analytics.economy(
	player: Player,
	flow: Enum.AnalyticsEconomyFlowType,
	currency: string,
	amount: number,
	endingBalance: number,
	transactionType: string,
	itemSku: string?
)
	if ENABLED and amount > 0 then
		AnalyticsService:LogEconomyEvent(player, flow, currency, amount, endingBalance, transactionType, itemSku)
	end
end

-- Custom event with an optional value and up to 3 custom fields.
function Analytics.custom(player: Player, eventName: string, value: number?, fields: { string }?)
	if not ENABLED then
		return
	end
	local customFields: { [string]: string } = {}
	if fields then
		local keys = {
			Enum.AnalyticsCustomFieldKeys.CustomField01.Name,
			Enum.AnalyticsCustomFieldKeys.CustomField02.Name,
			Enum.AnalyticsCustomFieldKeys.CustomField03.Name,
		}
		for i = 1, math.min(#fields, 3) do
			customFields[keys[i]] = fields[i]
		end
	end
	AnalyticsService:LogCustomEvent(player, eventName, value or 1, customFields)
end

-- isNewPlayer: true only for genuinely new players, so returning players don't
-- re-enter the onboarding funnel.
function Analytics.trackPlayer(player: Player, isNewPlayer: boolean)
	if isNewPlayer then
		loggedSteps[player] = {}
		Analytics.onboarding(player, "Joined")
	end
end

function Analytics.start()
	Players.PlayerRemoving:Connect(function(player)
		loggedSteps[player] = nil
	end)
end

return Analytics
```
<!-- /code -->

### What to instrument first

1. **Onboarding funnel (one-time).** Steps along your first session:
   1. Joined.
   2. Left spawn.
   3. Reached the first objective.
   4. Completed the first loop: got a reward, made a delivery, or hatched.
   5. First upgrade.
   6. Second loop.
   7. Tutorial done.

   Log only for **genuinely new** players.
2. **Economy events** at every source and sink: currency earned per activity, spent per shop item, Robux bundles (IAP).
3. **Shop funnel (recurring):** opened shop → viewed item → clicked buy → purchase prompt → purchased. Use a GUID `funnelSessionId` per shop open.
4. **Custom events** for core-loop health (e.g. `EggDelivered` with fields for egg rarity and size tier) and feature adoption.

## LiveOps toolkit

| Tool | Use |
|---|---|
| **Configs** (`production/configs`) | Change in-game values live without restarting servers (deploys over about 5 min): XP curves, prices, event toggles, feature flags. Read-only in game. |
| **Experiments** (`production/experiments`) | A/B test onboarding variants, prices, starting currency, UI placements, and measure causal impact |
| **Content cadence** | Major updates, QoL and fixes on a predictable schedule ([[Game Design for Roblox]]) |
| **Events** | Estimate the economy impact (EV) first; time-limited content; announce through in-game UI and notifications |
| **Alerts and dashboards** | Creator Hub analytics alerts and custom dashboards for crashes, error spikes and retention drops |

## Common mistakes

- Logging from the client, or testing in Studio and seeing nothing (both by design).
- A new event name per item or level, which hits the 100-name limit. Use custom fields.
- Re-logging onboarding for returning players, which pollutes the funnel.
- Changing step numbers after launch, which breaks history. Append new steps instead.
- No economy events, so there's no way to see inflation coming.

## Related notes

- [[Game Design for Roblox]]
- [[Monetization Design]]
- Hud's Guide:
  - [[Analytics and Metrics]]
  - [[Funnels and onboarding analysis]]
  - [[Retention, playtime and sessions]]
  - [[Monetization metrics]]

## Sources

- Roblox: [Funnel events](https://create.roblox.com/docs/production/analytics/funnel-events), [Economy events](https://create.roblox.com/docs/production/analytics/economy-events), [Custom events](https://create.roblox.com/docs/production/analytics/custom-events), [Custom fields](https://create.roblox.com/docs/production/analytics/custom-fields), [Event types and limits](https://create.roblox.com/docs/production/analytics/event-types), [Configs](https://create.roblox.com/docs/production/configs), [Experiments](https://create.roblox.com/docs/production/experiments). AUTHORITATIVE.
- *Hud's Guide*, chapters 02 and 08. USEFUL.

## Last Verified

2026-10-14
