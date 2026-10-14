---
title: Game Design for Roblox
category: Game Design
tags: [game-design, core-loop, onboarding, ftue, progression, economy, retention, liveops]
status: active
source_quality: AUTHORITATIVE (Roblox game-design docs) + COMMUNITY (Hud's Guide, linked)
last_verified: 2026-10-14
aliases: [Core loop, FTUE, Onboarding design, Progression design, Economy balancing, Rebirth, Collection design]
---

# Game Design for Roblox

## Summary

Roblox's official model of a **core loop** has three parts:
1. **Minute-to-minute interaction** (e.g. exploring);
2. the **most repeated set of actions** (e.g. fighting);
3. a **progression engine** (e.g. upgrades) that feeds back into both.

**Onboarding** succeeds when it:
- teaches the essentials (controls, and the *what* and *why* of the loop);
- gets to the fun quickly (fast early progression, social motivators, starter items);
- leaves players wanting more (visible short, mid and long goals, plus a moment of joy).

It is measured by **D1 retention** through a funnel.

For community-measured practice on Roblox (genres, benchmarks, what made recent hits), use **Hud's Guide**:
- [[Game Design, Genres and Live Ops]];
- [[Retention, Onboarding and Playtime]].

## Official principles

### Core loop

From `production/game-design/core-loops`:
- **The loop must be enjoyable on its own.** Progression multiplies fun; it doesn't replace it.
- **The progression engine must feed back** into the minute-to-minute and repeated actions: better gear means faster exploration and stronger fights.
- **A loop without progression is shallow; progression without a fun loop is a chore.**

### Onboarding (FTUE)

From `production/game-design/onboarding`:
- **Teach the essentials.** Controls, plus *what to do and why*.
- **Get to the fun quickly:**
  - low early XP thresholds so players level up fast;
  - social motivators;
  - starter items and currency.
  - Tune these live with **Configs**, and test variants with **Experiments**.
- **Leave players wanting more:**
  - short-, mid- and long-term goals, surfaced visibly (quests, collections, passes);
  - **end onboarding on a moment of joy**.
- **Measure it** with an onboarding funnel ([[Analytics and LiveOps Guide]]).

### Virtual economy

From `production/game-design/balance-virtual-economies`:
- **Track the data:** who buys what, which actions yield currency, earned vs bought, usage timing.
- **Plan resources:** every new **source** needs matching **sinks**. The doc's example is a 24-hour Golden Salmon event at 0.1% odds, worth 1,000 Gold: increased fishing during the event floods the economy and purchases stall.
- **Estimate impact with expected value (EV):** the sum of value × probability across outcomes, times the number of attempts per day. Compare event days with normal days before launching.
  - Doc erratum: the doc's worked example lists Salmon as "30 G × 30% = 3 G". The 3 G result matches the stated 10% catch chance, so 30% is a typo.
- **Sinks:** limited-time cosmetics, upgrades, rebirth costs, consumables, fees.
- **Watch the Economy dashboard.** Total sources minus total sinks should be close to 0. A growing average wallet (especially for payers) means add sinks.

### Live ops

From `liveops-essentials`, `content-updates` and `liveops-planning`:
- Keep a regular **content cadence**: major updates, quality-of-life fixes and bug fixes.
- **Plan events against the economy** (EV first).
- Use **Configs** for live tuning and **Experiments** for A/B tests (`production/configs`, `production/experiments`).

## Design patterns that recur in successful Roblox games

All community-observed; see Hud's Guide for evidence and counter-evidence:
- **A clear fantasy in one sentence**, which shows in the thumbnail too ([[Show the core fantasy and loop]]).
- **Fast first reward.** The first meaningful reward comes within the first minute, with visible progress after that ([[Tutorial fundamentals]], [[Bounce rate and first-session fixes]]).
- **Collection and rarity.** Eggs or pets, cards and other collections, with escalating reveal moments. Variable rewards drive "one more" ([[Core loop, progression and content depth]]).
- **Rebirth / prestige** resets progress for permanent multipliers, extending a simple loop ([[Progression, rebirths and balancing]]).
- **Social visibility.** Others see your big pets or eggs, leaderboards and plots. Status drives effort.
- **Appointment mechanics.** Timers (hatch, grow, daily rewards) bring players back, but can frustrate if they are too long early.
- **Stealing beats defending** as a fantasy ([[Show the core fantasy and loop]]).

## Design checklist for a new feature

1. Which part of the loop does it serve (minute-to-minute, repeated action, or progression)?
2. What is the player's goal, and how do they see their progress?
3. What source and sink does it add? Run the EV estimate.
4. What is the moment of joy?
5. What's the exploit surface ([[Remote Security and Anti-Exploit]])?
6. How will we measure it (funnel step or custom event)?
7. Does it read on mobile, and in a thumbnail?

## Related notes

- [[Monetization Design]]
- [[Analytics and LiveOps Guide]]
- [[UI Visual Design and Polish]]
- Hud's Guide hubs:
  - [[Game Design, Genres and Live Ops]]
  - [[Retention, Onboarding and Playtime]]
  - [[What makes a hit]]
  - [[Finding and validating ideas]]

## Sources

- Roblox: [Core loops](https://create.roblox.com/docs/production/game-design/core-loops), [Onboarding](https://create.roblox.com/docs/production/game-design/onboarding), [Balance virtual economies](https://create.roblox.com/docs/production/game-design/balance-virtual-economies), [LiveOps essentials](https://create.roblox.com/docs/production/game-design/liveops-essentials), [Configs](https://create.roblox.com/docs/production/configs), [Experiments](https://create.roblox.com/docs/production/experiments). AUTHORITATIVE.
- *Hud's Guide To Roblox Success*, chapters 08 and 10 (community observations, May–Oct 2026). USEFUL / COMMUNITY OBSERVATION.

## Last Verified

2026-10-14
