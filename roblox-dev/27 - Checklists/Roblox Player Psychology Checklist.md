---
title: Roblox Player Psychology Checklist
category: Checklists
tags: [checklist, psychology, retention, onboarding, monetization, rng, community, tasks]
status: active
source_quality: Distilled from Roblox Player Psychology Guidelines (community deck; odds rules AUTHORITATIVE); game facts PROJECT-VERIFIED (2026-10-14 Studio report)
last_verified: 2026-10-14
aliases: [Psychology checklist, Player psychology tasks, Retention checklist]
---

# Roblox Player Psychology Checklist

## Summary

**Part A** is a reusable checklist for any Roblox game. **Part B** applies it to Roll and Steal an Egg! as a task list. The reasoning behind each line is in [[Roblox Player Psychology Guidelines]].

Work in the deck's order:
1. Fix Day 1.
2. Build Day 7.
3. Add pricing.
4. Community.

Don't start a later stage while an earlier one is failing its target.

## Part A: general checklist

### 0. Measure first
- [ ] Onboarding funnel events are logged server-side, once per new player, in order ([[Analytics and LiveOps Guide]]).
- [ ] Economy events (sources and sinks) and progression events are logged.
- [ ] You know your D1, D7, D30, session length, payer conversion and ARPPU, and compare them with your genre benchmark in Creator Analytics.
- [ ] You have named the one number you are failing, and the lever for it (the table in the guidelines, section 1).

### 1. Day 1: the first minute
- [ ] 0–5 s: the player spawns inside the world. No logo, cutscene, dialogue or forced UI.
- [ ] 5–10 s: the player can do the core verb.
- [ ] 10–30 s: the first reward lands (reciprocity).
- [ ] 30–45 s: a next milestone is on screen and looks close (Zeigarnik, goal gradient).
- [ ] 45–60 s: a free, expiring taste of premium (e.g. a timed boost).
- [ ] Progress never starts at zero (endowed progress).
- [ ] One mechanic, one button, one goal on screen. Hide or defer every other HUD element (cognitive load).
- [ ] The thing to click is the only thing glowing (isolation effect).
- [ ] The best effect of the game is seen in minute one; the tutorial ends on a peak with a reward (peak-end).
- [ ] No shop, Robux offer, like or favourite prompt before the first reward.
- [ ] No wall of tutorial text; direction is taught by beams, corridors and glow.
- [ ] The goal is visible before any walk; no long empty walk to the fun.
- [ ] Join performance is checked on a low-end phone ([[Performance Checklist]]).
- [ ] Failing in minute one is cheap and instant, never a full reset.

### 2. Day 7: staying rewarding
- [ ] All four reward schedules are layered: fixed ratio (drip), variable ratio (drops), fixed interval (timers, dailies), variable interval (random events).
- [ ] A milestone ladder with no dead ends: there is always a visible next goal, shown as nearly reached.
- [ ] A collection to complete (an index or sets), with its incompleteness visible.
- [ ] Something the player builds or arranges (IKEA effect).
- [ ] Offline timers hand the player an unfinished job on exit, and the return screen states it specifically ("Back in 4h 32m").
- [ ] Totals are shown (playtime, total collected), so the investment is visible.
- [ ] A daily hook with a safety valve: one missed day is forgiven, not a full streak reset.
- [ ] At least one social feature (a shared goal, a clan, a plaza, a leaderboard).

### 3. Multiplayer and solo
- [ ] Solo play works at any server population (autonomy, competence, relatedness).
- [ ] Rare items and results are visible to other players (conspicuous consumption, social proof, FOMO).
- [ ] There is a shared space where results are shown off (the Pet Simulator 99 / Grow a Garden template).
- [ ] Servers never look empty to a new player (server size matches the content).

### 4. Pricing
- [ ] A 49–99 R$ first-purchase item exists.
- [ ] The pass ladder covers four bands: 49–99 threshold, 199–399 convenience, 499–799 luck and power, 999+ identity.
- [ ] Convenience is sold before power. Nothing sells raw victory.
- [ ] Prices end in 9 for value items (charm pricing) and are round for identity items.
- [ ] The shop shows the most expensive option first (anchoring); a decoy option sits next to the target.
- [ ] Bundles are used to hold margin.
- [ ] The best pass is not underpriced.
- [ ] Conversion and ARPPU are tracked separately.
- [ ] Optional: a weekly infinite pack (rising-price ladder with free rungs) — only after the above.

### 5. RNG and pity (do this last)
Policy (AUTHORITATIVE, [Paid random items](https://create.roblox.com/docs/production/monetization/paid-random-items)):
- [ ] Every paid random outcome (bought with Robux or with Robux-bought currency) shows all outcomes and numeric odds that sum to 100%, before purchase.
- [ ] Paid luck boosts and pity systems state their effect in numbers before purchase, and the live odds update while active.
- [ ] `PolicyService:GetPolicyInfoForPlayerAsync()` is checked: `ArePaidRandomItemsRestricted` users get no paid random items (offer direct purchase); `IsPaidItemTradingAllowed` gates trading.
- [ ] Every outcome gives the player something (deck claim; re-check the live policy page).

Design:
- [ ] About five rarity tiers; one colour per tier, never reused elsewhere.
- [ ] Spectacle scales steeply with rarity; the top tier is clip-worthy.
- [ ] The whole pool is shown, not a mystery.
- [ ] A visible pity counter, one per rarity, never reset quietly.
- [ ] The reveal animation builds anticipation and uses near misses honestly.
- [ ] Odds and counters never change without an announcement.

### 6. Community
- [ ] A shareable moment exists (rare pull, huge number, dramatic failure), and a place to share it.
- [ ] Discord join gives an in-game reward; no bare invites.
- [ ] Discord roles for rare owners; volunteer jobs (testers, bug hunters, event mods); a wins channel.
- [ ] In-game recognition for creators' clips.
- [ ] Giveaways need a small effort to enter, and winners are always shown.
- [ ] Each drop has a teaser ramp (silhouettes, one reveal a day).
- [ ] Small creators (10k–100k subscribers) get codes or exclusives first.
- [ ] Updates ship on a regular weekly rhythm; content rotates, not only grows; the school calendar is in the plan.

### 7. Ethics gate (every stage)
- [ ] No mechanic is designed to make stopping feel impossible (about a third of age-checked users are under 13).
- [ ] Streaks forgive a missed day; FOMO timers are honest; no dark patterns in purchase flows.

## Part B: Roll and Steal an Egg! task list

**Facts come from the 2026-10-14 Studio report**, computed pass-free:
- spawn on the plot porch, 15 HUD elements at 0:00;
- the first steal at ~0:10;
- a guaranteed Epic Owl (~$85/s) at ~0:40;
- tutorial complete at ~8:00, with no reward.

The build plan is `handoffs/2026-10-14-first-10-minutes-plan.md` ([GitHub](https://github.com/apollo9360/roll-egg-notes/blob/claude/stoic-lamport-jqadwi/handoffs/2026-10-14-first-10-minutes-plan.md)). Items marked **(plan P…)** are already in it.
- **Decisions D1–D7 need the user's answer** before they are built.
- Items marked **NEW** came from this checklist.
- Items marked **VERIFY** need a Studio check first.

### Stage 1: fix Day 1
- [x] Onboarding funnel events (`OnboardingService`). Already built; check the step list matches the new tutorial once P1.1 lands.
- [ ] Economy and progression events: `LogEconomyEvent` for wheel, pen and trail purchases, and `LogProgressionEvent` for zone unlocks (plan P2.4).
- [ ] Clean the HUD at 0:00 to money, Speed and the tutorial card; pop the rest in later. This covers cognitive load and the isolation effect.
- [ ] Move the "TIP: Traps stop guards too…" pill off the "Meadow nest: N studs" line. The isolation effect needs one goal on screen.
- [ ] First-timer guard grace with a near miss (plan P1.2, D5). Today a pause after the grab is caught in ~1.5 s and resets the tutorial.
- [ ] Night doesn't wipe tutorial players (plan P0.2, D6).
- [ ] Max Players matches the 5 plots (plan P0.1, D1). Otherwise a 6th player can never finish step 3.
- [ ] Fix the step 2 text ("Kite the Mother Hen…" is no longer rewarded) and add size-milestone pops while rolling. This is endowed progress: the egg visibly gets better.
- [ ] Give the Epic Owl hatch a full reveal beat, and place the pet automatically. This is the peak-end rule in minute one.
- [ ] Remove the two 120 s screen-dimming chores (Index and Daily) from the first 3 minutes (plan P1.1, D3). Make the Index claim required, because it unlocks Riverbank.
- [ ] Taste of premium at 45–60 s, or at tutorial end: a free **2× Luck for 10 minutes** (plan P1.7, D4). It reuses the existing Luck boost, and the Luck15 product (49 R$) sells the same thing later.
- [ ] End the tutorial on a peak: confetti and the reward, then an always-visible **Next Goal** chip (plan P1).
- [ ] Favourite prompt only after the first big win (the first Legendary or 9 minutes), never at join (plan P2.3).
- [ ] Phone text size in the tutorial card (plan P2.1).
- [ ] **NEW:** check join load time on a low-end phone. The report found 33 unauthorised sound loads and 6 failed TexturePack uploads per session (plan P2.5 covers the sounds).
- [ ] Target: D1 ≥ 20%.

### Stage 2: build Day 7
- [x] Collection: the Index pays Speed and cash per new pet; completing a zone unlocks that zone's bat skin.
- [x] Appointments: the egg reset every 5 minutes, live events (Capture the Egg every 20 min, Egg Golem every 30, Night Run every 45, HUGE EGG every 3 resets), and 3 daily quests.
- [x] A 7-day Daily with a big Day 7 prize (15 min income, +400 Speed, 15 min 2× Luck).
- [ ] **VERIFY / NEW:** does missing a day reset the Daily cycle? If so, add one forgiven day (safety valve).
- [ ] **VERIFY / NEW:** offline earnings (50%, capped at 8 h) — where are they shown on return? Show a specific "While you were away: $X" moment, and on exit state when the cap fills ("Back in 7h 12m for the full $X").
- [ ] **NEW:** show the Index as nearly complete (e.g. "5 / 7 Meadow pets"), with the next silhouette visible (goal gradient, show the whole pool).
- [ ] **NEW:** sunk-effort stats on the profile or plot: total eggs stolen, biggest egg, total earned, playtime.
- [x] One social feature: lobby leaderboards (Biggest Egg, Top Earners, Most Rebirths) and the BIGGEST EGG TODAY board with a 3D holder.
- [ ] **NEW:** make rare pets and eggs visible to other players: a server announcement on a Golden Goose, Eggbert or Rainbow hatch, and a carried HUGE egg that is readable from across the map (conspicuous consumption, FOMO).
- [ ] Target: D7 ≥ 8%.

### Stage 3: pricing

Current products, from `Config.Products`:

| Product | Price (R$) |
|---|---|
| Starter 2× Speed (15 min) | 3 |
| Luck 15 / 30 / 60 min | 49 / 99 / 199 |
| Money 30 min | 99 |
| Speed 60 min | 129 |
| Speed Packs | 49 / 129 / 349 |
| Instant Hatch | 9 / 49 / 149 / 299 |
| Trails | 19 / 29 / 49 / 79 / 119 / 199 |
| Traps5 | 25 (product id not set) |
| Pass 2× Money | 399 |
| Pass 2× Growth | 467 |

- [x] A threshold-breaker exists (Starter 3 R$; several 49 R$ items).
- [ ] **NEW:** check the ladder's bands:
  - 199–399 convenience is thin (2× Money 399 is closer to power);
  - 499–799 luck and power has **nothing**;
  - 999+ identity has **nothing**.
  Candidates for the user to decide: a permanent 2× Luck pass (499–799), and a VIP or identity cosmetic (999, round).
- [ ] **NEW:** charm pricing: 467 reads as precise (fine), and 3 R$ is far below the deck's 49–99 band. Ask the user whether 3 R$ is intentional (a loss-leader) before changing anything.
- [ ] **NEW:** order the Shop most expensive first (anchoring), with a decoy beside the main pass.
- [ ] **VERIFY / POLICY:** Luck15/30/60 are paid **probability modifiers**. The policy needs the boost's effect stated in numbers before purchase, and the live odds shown while it is active. Check whether the Shop or the egg UI shows the rarity table (Chick 40% … Eggbert 0.05% at size 1.0) and the boosted version. If not, build an odds panel.
- [ ] **VERIFY / POLICY:** Instant Hatch (Robux) skips time but should not change the outcome. Confirm in code that it doesn't affect odds.
- [ ] **VERIFY / POLICY:** check `PolicyService` `ArePaidRandomItemsRestricted` before selling Luck products, and gate them for restricted users.
- [ ] Set the Traps5 product id (it reads 0), or hide the item (Creator Hub item: give the user the list; never click through it).
- [ ] Track conversion and ARPPU separately (Creator Analytics).

### Stage 4: RNG and pity polish
- [x] Rarity, variants and sizes exist:
  - 7 Meadow pets, plus Riverbank and Canyon;
  - egg variants Plain to Rainbow (1%);
  - size tiers Big, Huge and Colossal;
  - mutations Silver, Golden and Rainbow.
- [ ] **NEW:** one colour per rarity tier across the HUD, hatch card, Index and leaderboards. Check that no tier colour is reused for buttons.
- [ ] **NEW:** spectacle scales steeply. An Eggbert (0.05%) hatch should be roughly ten times a Golden Goose hatch: a server-wide moment, clip-worthy.
- [ ] **NEW (user decision):** a visible pity counter per rarity, e.g. "Golden Goose guaranteed in 34 eggs". If Luck products stay paid, the pity system counts as a probability modifier and must be disclosed.
- [ ] **VERIFY:** mutation chances weren't found in `Config.Mutations`. Find where they're rolled, and include them in any odds display.

### Stage 5: community
- [ ] **NEW:** a Discord or group join reward in-game (e.g. a trail colour or a Luck boost), never a bare invite. Ask the user whether a community exists yet.
- [ ] **NEW:** a "wins" moment: the server announcement plus a screenshot-friendly hatch card for rare pulls.
- [ ] **NEW:** a teaser ramp before each zone or pet drop (silhouette in the Index, one reveal a day).
- [ ] **NEW:** a weekly update rhythm, with rotating limited eggs or trails rather than only adding.
- [ ] Target: D30 ≥ 3%.

### Ethics gate for this game
- [ ] Night, guards and timers punish a first-timer gently (covered by D5 and D6).
- [ ] The Daily forgives a missed day.
- [ ] Every Robux button keeps its in-modal confirm (it already does: "Buy X for R$ N?").

## Related notes

- [[Roblox Player Psychology Guidelines]]
- [[Game Design for Roblox]]
- [[Monetization Design]]
- [[Analytics and LiveOps Guide]]
- [[Release Checklist]]

## Sources

- [[Roblox Player Psychology Guidelines]] (community deck by balthy3434, August 2026). USEFUL / COMMUNITY OBSERVATION.
- [Paid random items policy guidelines](https://create.roblox.com/docs/production/monetization/paid-random-items). AUTHORITATIVE.
- The 2026-10-14 first-10-minutes Studio report (product list, rarity table, timers, Daily, Index) and `handoffs/2026-10-14-first-10-minutes-plan.md`. PROJECT-VERIFIED.

## Last Verified

2026-10-14
