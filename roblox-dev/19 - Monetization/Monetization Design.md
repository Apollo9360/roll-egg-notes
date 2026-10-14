---
title: Monetization Design
category: Monetization
tags: [monetization, game-passes, developer-products, subscriptions, shop, pricing, ethics]
status: active
source_quality: AUTHORITATIVE (Roblox monetization docs) + COMMUNITY (Hud's Guide, linked)
last_verified: 2026-10-14
aliases: [Game Passes, Developer Products, Subscriptions, Starter packs, Shop design, Price testing, Ethical monetization]
---

# Monetization Design

## Summary

**Product types:**
- **Game Passes**: one-time, permanent perks. Check ownership with `UserOwnsGamePassAsync`.
- **Developer Products**: repeatable, consumable. Grant through `ProcessReceipt` ([[Developer Product Receipt Pipeline]]).
- **Subscriptions**: recurring benefits.
- Plus private servers, paid access, avatar items, immersive and rewarded ads, Creator Rewards.

**Roblox's design guidance:**
- Sell **enhancement** (speed, protection, access) and **expression** (skins, emotes, pets) items.
- Use a shop that is **integrated, contextual and inviting**.
- Run **season passes** with free and premium tracks.
- Test prices with **Experiments**; use **regional and managed pricing** where available.

The community playbook is in **Hud's Guide → [[Monetization]]**: starter packs, price ladders, placement, and conversion vs ARPPU.

## Official design guidance

From `production/game-design/monetization-foundations`:
- **Item types:**
  - **durable** (unlimited use, e.g. cosmetic skins);
  - **consumable** (limited uses, e.g. boosts).
- **Item purposes:**
  - **enhancement** (improves the experience: speed, protection, strength, tools, event-mode access);
  - **expression** (personalization: skins, emotes, pets).
- **The shop should be:**
  - **integrated**, as part of the world and flow;
  - **contextual**, offered when relevant (e.g. revive on death: a "contextual purchase");
  - **inviting**, attractive and easy to browse.
- **Season passes:**
  - free and premium tracks;
  - attractive rewards;
  - **manageable timeframes**, so players can actually finish.

## Implementation map

| Product | API / flow | Notes |
|---|---|---|
| Game Pass | `MarketplaceService:PromptGamePassPurchase`; ownership via `UserOwnsGamePassAsync` (cache it on join); grant mid-session on the **server** in `PromptGamePassPurchaseFinished`, then re-verify | Permanent. Never record as consumable. |
| Developer Product | `PromptProductPurchase`; **grant only in `ProcessReceipt`** | [[Developer Product Receipt Pipeline]] |
| Subscription | `PromptSubscriptionPurchase` plus subscription status checks (see `production/monetization/subscriptions`) | Recurring perks; design for retained value |
| Private servers, paid access | Configured in Creator Hub | |
| Rewarded video ads | Eligibility thresholds apply (see the docs and Hud's Guide) | Usually only for large games |
| Regional / managed pricing | Creator Hub settings (`regional-pricing`, `managed-pricing`, `price-optimization`) | Test before rolling out |

**Security:** all grants are server-side. Never trust client purchase-finished signals for consumables ([[Remote Security and Anti-Exploit]]).

## Ethics and long-term value

Practice notes, aligned with Roblox policy:
- **Sell solutions to real problems the game creates**: time, convenience, power within reason, self-expression ([[Product design sell solutions to real problems]]).
- **Don't** use dark patterns aimed at kids:
  - misleading prices or currency conversions;
  - fake scarcity;
  - pay-to-skip on artificially frustrating timers right at onboarding.

  They hurt retention and trust, and some violate the rules ([[Terms of Service limits]]).
- **Paid random items** (loot boxes) have specific Roblox requirements (`production/monetization/paid-random-items`). Read them before shipping any Robux-for-random-reward mechanic.
- **Keep the free path fun.** Monetization multiplies a good loop; it can't replace one.

## UX of purchasing

- **Soft and premium currency** visually distinct ([[UI Visual Design and Polish]]).
- **Show exactly what the player gets** (preview), the price, and the value comparison (bundles).
- **Confirm expensive purchases.** Disable buttons while data is not loaded.
- **Celebrate the purchase** with a moment of joy, and make the benefit visible immediately.

## Related notes

- [[Developer Product Receipt Pipeline]]
- [[Game Design for Roblox]]
- [[Analytics and LiveOps Guide]] (economy events, monetization funnels)
- Hud's Guide:
  - [[Monetization]]
  - [[Cheap entry items and starter packs]]
  - [[Price ladder and anchoring]]
  - [[Placement in-world, menus and popups]]
  - [[Core metrics payer conversion vs ARPPU]]

## Sources

- Roblox: [Monetization overview](https://create.roblox.com/docs/production/monetization), [Monetization foundations](https://create.roblox.com/docs/production/game-design/monetization-foundations), [Passes](https://create.roblox.com/docs/production/monetization/passes), [Developer products](https://create.roblox.com/docs/production/monetization/developer-products), [Subscriptions](https://create.roblox.com/docs/production/monetization/subscriptions), [Paid random items](https://create.roblox.com/docs/production/monetization/paid-random-items), [Price optimization](https://create.roblox.com/docs/production/monetization/price-optimization). AUTHORITATIVE.
- *Hud's Guide*, chapter 09 (community). USEFUL.

## Last Verified

2026-10-14
