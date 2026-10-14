# Request: brutal critic review of Roll and Steal an Egg!

**Date:** 2026-10-14
**Requested by:** the user, via the cloud session that maintains `roblox-dev/`.
**Run by:** a local Claude Code session connected to Roblox Studio through MCP.
**Repo:** https://github.com/apollo9360/roll-egg-notes, branch `claude/stoic-lamport-jqadwi`. Commit locally; the user pushes from GitHub Desktop.

## What we want

A full, honest critic review of the game, in the style of the user's earlier audit of another game, *"Break The Piggy Bank — Brutal Algorithm Audit"*. That audit worked because it was:
- **ranked by impact on Roblox's ranking signals:** bounce rate, playtime, play days, co-play, spend;
- **built on evidence:** script paths, line numbers, config values, and numbers computed from the code (time to first payoff, DPS, waits between purchases);
- **written from a kid's point of view:** "what a confused kid experiences", second by second;
- **concrete:** every problem has a fix with real numbers, plus **Effort** (XS / S / M / L) and **Impact** (Low / Med / High / Very high);
- **fair:** every section ends with "What's good".

Write it for the user and their team, not for a developer audience only. Plain language, short sentences, tables where they help.

## Rules

- **Read-only.** Don't change scripts, instances, configs, data or the map.
- **Playtests:** ask the user first. The user may already have allowed one for the first-10-minutes report; if they did, reuse it.
- **Evidence labels.** Tag every important claim as one of:
  - **Measured:** seen in a playtest or the console;
  - **Computed:** derived from code or config, so show the formula;
  - **Static:** read from code without running it;
  - **Opinion:** your judgement.
  Never present an opinion as a fact.
- **No invented data.** The game has no analytics funnel yet (or only a new one). Don't make up funnel numbers. Where the reference audit used Creator Dashboard data, use a **simulated new-player timeline** computed from the configs instead, and say so.
- **Consult-only:** never copy from `ServerStorage.REFERENCE_PARKED`, `Quarantine`, `Snapshots_*`, `W1_Backups` or the Steal an Egg decomp. You may compare our game with the reference game to explain genre expectations; label it as the reference.
- **Don't open Creator Hub.** For live product prices, use `MarketplaceService:GetProductInfo` inside `execute_luau`, or say "not checked".
- **Audience:** kids roughly 7–12, mostly on phones, arriving from ads aimed at Simulation and Party/Casual players. Judge everything against that player.

## Inputs to read first

1. `handoffs/2026-10-14-first-10-minutes-report.md`, if it exists. Reuse its facts and screenshots; don't redo that work.
2. `DEV_NOTES.md`: the standing rules and the latest entries.
3. `KEY_VISUALS.md` (in the scratch workspace; ask the user if you can't find it): the intended look, dream pets, size-flex tiers.
4. The vault, for the yardsticks the review should hold the game to:
   - `huds-guide/08 Retention, Onboarding and Playtime/`: benchmarks, first-session fixes, visual guidance, contextual steps, funnel analysis;
   - `huds-guide/04 Algorithm and Home Recommendations/`: what the algorithm ranks on;
   - `huds-guide/09 Monetization/`;
   - `roblox-dev/18 - Game Design/Game Design for Roblox.md`, `roblox-dev/19 - Monetization/Monetization Design.md`;
   - `roblox-dev/27 - Checklists/`: UI, Mobile UI, Remote Security, Performance, Production Readiness.

## How to work

1. **Build a fact base first** (`handoffs/critic-review-facts.md`, a scratch file you may commit):
   - every Config module in full;
   - every server service and client controller, with one line on what it does;
   - every remote, and who it trusts;
   - every product and gamepass with its price;
   - the tutorial steps;
   - instance counts.
   Each coach below works from this, so the numbers agree across sections.
2. **Run the coaches as independent reviewers.** If you can run subagents with Studio access, give each coach to its own subagent with the fact base. Otherwise do them one after another, and don't let an earlier coach's conclusion steer a later one.
3. **Synthesise.** Write the TL;DR and the master action plan last, from where the coaches agree.

## Report structure

Write to `handoffs/2026-10-14-critic-review.md`. Put screenshots in `handoffs/critic-review-screenshots/`.

### TL;DR: the 10 fixes that matter most
- One paragraph with the single biggest diagnosis, as the reference audit did ("the one big dopamine moment reaches only X%…").
- The average score across coaches.
- Then the 10 fixes, ranked by impact on the ranking signals. One bold sentence each, plus the key number.

### What the data says
- If a funnel exists, chart or table it, and name the cliffs (any step that loses 15% or more).
- If not, give the **simulated timeline**: for a new player who always does the obvious next thing, the time to the first steal, first safe line, first hatch, first purchase, first wheel, first Riverbank unlock and first rare pet.
- Say plainly where the game would lose players, and why.

### The coaches

Each coach section contains:
- a **score out of 10** and a two-sentence verdict;
- **"The kid's experience":** a short time table (Time | What happens | Risk) or day-by-day story;
- **Problems, ranked:** each with evidence (path, line, values), the fix with concrete numbers, then *Effort · Impact*;
- **What's good.**

Use these coaches, adapted to this game:

1. **First 60 seconds and tutorial.**
   - Spawn framing, the walk to the nest, the guidance, the UI on screen at second 0, and popups.
   - Is each tutorial step completed by the player's own action, or can it finish without them?
   - When does onboarding count as done, and is it saved? What happens on rejoin?
   - What do the top games in the genre do in their first 60 seconds that this one doesn't?
2. **Core loop: steal, roll, escape.**
   - How grabbing feels; how the guard wakes and chases; the danger curve; the safe line.
   - Does rolling the egg (the game's hook) *feel* like growth, second to second?
   - Hit-stop, shake, sound, numbers. Could a kid fail the first run, and what then?
3. **Egg growth, luck and the hatch payoff.**
   - Growth rate, caps, egg variants, the clover luck and its readability.
   - Is the hatch reveal a big enough dopamine hit? Rarity presentation.
   - Is "bigger egg = better pet" visible and believable? Size-flex tiers.
   - Use the drop tables and luck formula to compute the odds of each rarity in the first 10 minutes.
4. **Economy and pacing.**
   - A progression timeline computed from the code (Min 1 / 5 / 15 / 30 / Hour 1 / Hour 2): income, the next buy, and the wait.
   - Dead zones where the wait is over about 2 minutes early on.
   - Do numbers grow fast enough for this genre ("×10 every 5–10 minutes")?
   - Are there purchases that change nothing visible?
5. **Progression systems.**
   - Speed and how it is gained; hamster wheel tiers; zone unlocks (300 / 8,000 Speed); Index rewards; rebirth; trails.
   - Does each system feed the core loop, or sit beside it?
   - Is there always a visible next goal?
6. **Zones, guards and world challenge.**
   - Meadow / Riverbank / Canyon: variety, difficulty curve, and guard readability (sleeping, then awake).
   - Night, the 5-minute egg reset, and the HUGE EGG status.
   - Does the second zone feel new, or just harder?
7. **Monetisation.**
   - The current catalogue in a table (Product | Price | Where shown | Verdict), with live prices if checked.
   - Under- and over-pricing, the spending ceiling, missing gamepasses or a Starter Pack.
   - Prompts during the tutorial; Robux buttons in thumb zones.
   - **Copy that could mislead kids** (a policy risk). Paid boosts lost on rejoin or overwritten.
8. **Retention and return loops.**
   - The kid's story on Day 1, Day 2 and Day 7.
   - Daily rewards: who pays them (the server or the client), calendar days or rolling timers.
   - Quests, offline earnings and the Welcome Back card, the favourite prompt, experience notifications, appointment events.
9. **Social and co-play.**
   - Do other players make the game better or worse for a newcomer (stealing, blocking, griefing)?
   - Do friends get a visible payoff? Invites, LaunchData, squad bonuses.
   - Show-off moments: the Biggest Egg board, pets on plots, server announcements.
   - Any bots or fake players? If so, flag them as a trust and moderation risk.
10. **HUD, UI clarity and mobile.**
    - A screen map on a phone (Element | Phone size | Call | Why).
    - Touch targets of at least 44 pt; text of at least 14 pt on phones.
    - Overlaps, thumb zones, consistent words for the same thing, fonts.
    - Menus hiding the HUD; the Goal or Next chip.
11. **World, first impression and performance.**
    - Frame by frame from join: loading, the first view, what streams in late.
    - Part and instance counts; per-frame scripts; lights and shadows.
    - Join payload; coin, egg and VFX counts at peaks; mobile quality scaling.
    - Store page: give our current thumbnail direction a verdict, using `roblox-dev/21 - Discovery & Marketing/Free AI Image Tools and Thumbnail Prompts.md`.
12. **Trust and security.**
    - Remotes that trust the client (money, Speed, egg size, placement, rewards, daily claims).
    - Exploit paths a kid with an executor could find in a day.
    - Race conditions in carrying and placing eggs, and dupes on rejoin.
    - Use `roblox-dev/27 - Checklists/Remote Security Checklist.md` and its "exploit pass" as the yardstick. Don't run any exploit against the live game; reason from code.

### Master action plan
Order by impact ÷ effort, as checklists:
- **This week:** quick wins, mostly under an hour each.
- **Next 2 weeks:** the core rebuild.
- **Later:** depth for D7–D30.

Put **analytics first**: the funnel steps to log, so the fixes can be measured.

## Deliver

1. Commit the report, fact base and screenshots on `claude/stoic-lamport-jqadwi`.
2. Tell the user it's ready to push.
3. Don't start any fixes. The user will decide which ones to build.
