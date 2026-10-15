# Build request: work through the player psychology checklist (Roll and Steal an Egg!)

**Date:** 2026-10-14
**Written by:** the cloud session.
**Built by:** a local Claude Code session connected to Roblox Studio (place 122907972658396).
**Repo:** https://github.com/apollo9360/roll-egg-notes, branch `claude/stoic-lamport-jqadwi`.
- If your clone lacks the branch: `git fetch origin claude/stoic-lamport-jqadwi`, then `git checkout claude/stoic-lamport-jqadwi`. If you have it already, `git pull`.
- Commit locally; the user pushes from GitHub Desktop.

**Paste line for the user:** `Read handoffs/2026-10-14-psychology-checklist-build.md on branch claude/stoic-lamport-jqadwi (git fetch first) and do what it says.`

---

## 1. What you're doing and why

A checklist turned a player-psychology deck into tasks for this game. Your job is to work through **Part B** of it:
1. Audit what is true in Studio today.
2. Get the user's decisions in one message.
3. Build the answered items in stage order.
4. Tick the boxes and report.

**The two files** (read Part B of the checklist in full; use the guidelines only to look up *why* an item exists):

| File | Path | GitHub |
|---|---|---|
| Checklist (your task list) | `roblox-dev/27 - Checklists/Roblox Player Psychology Checklist.md` | [link](https://github.com/apollo9360/roll-egg-notes/blob/claude/stoic-lamport-jqadwi/roblox-dev/27%20-%20Checklists/Roblox%20Player%20Psychology%20Checklist.md) |
| Guidelines (background) | `roblox-dev/18 - Game Design/Roblox Player Psychology Guidelines.md` | [link](https://github.com/apollo9360/roll-egg-notes/blob/claude/stoic-lamport-jqadwi/roblox-dev/18%20-%20Game%20Design/Roblox%20Player%20Psychology%20Guidelines.md) |

**Scope:**
- **In scope:** every Part B line marked **NEW** or **VERIFY**, plus the unmarked lines that aren't tagged "(plan P…)".
- **Out of scope:** lines tagged "(plan P…)". They belong to `handoffs/2026-10-14-first-10-minutes-plan.md` ([GitHub](https://github.com/apollo9360/roll-egg-notes/blob/claude/stoic-lamport-jqadwi/handoffs/2026-10-14-first-10-minutes-plan.md)), which has its own decisions D1–D7.
  - Record only whether each is built or not.
  - If the user tells you to build them as well, follow that file, not this one.
- **Not code (list them for the user, don't build):** a weekly update rhythm, creator relationships, Discord server setup, and anything in Creator Hub.

## 2. Facts you need (from the 2026-10-14 Studio report, pass-free)

**The test account owns both passes and is an admin,** so observed income and growth are 2×.

**Products** (`ReplicatedStorage.Shared.Config.Products`). Every Robux button opens an in-modal confirm ("Buy X for R$ N?"). The Shop sells no cash items.

| Id | Kind | What | R$ |
|---|---|---|---|
| Starter2xSpeed | Product, once | 2× Speed 15 min | 3 |
| Pass2xMoney / Pass2xGrowth | Pass | 2× Money / 2× Egg Growth, forever | 399 / 467 |
| Luck15 / Luck30 / Luck60 | Product | **2× Luck** 15 / 30 / 60 min | 49 / 99 / 199 |
| Money30 | Product | 2× Money 30 min | 99 |
| Speed60 | Product | 2× Speed 1 h | 129 |
| SpeedS / M / L | Product | Speed packs | 49 / 129 / 349 |
| HatchT1–T4 | Product | Instant Hatch (≤2 min / 10 min / 30 min / any) | 9 / 49 / 149 / 299 |
| Traps5 | Product | +5 Traps, **product id 0 (not set)** | 25 |
| Trail_Green … Trail_Rainbow | Product | Trail tiers 1–6 | 19 / 29 / 49 / 79 / 119 / 199 |

**Odds:**
- Pet drop weights live in `Curves.LuckAdjustedTable`: weight × rel^min(tier−1, 4), where rel = luck relative to the zone's LuckBase, capped at 5.
- Egg size raises luck. Meadow at size 1.0: Chick 40%, Bunny 28%, Lamb 18%, Fox 9%, Owl 4%, Golden Goose 1%, Eggbert 0.05%.
- Egg variants (`Config.EggVariants`, weights /100): Plain 44, Speckled 22, Spotted 14, Marbled 10, Shiny 6, Golden 3, Rainbow 1.
- Mutations (Silver ×1.25, Golden ×2, Rainbow ×3): **the chances were not found** in `Config.Mutations`.

**Retention systems:**
- **Daily** (`Config.Retention.Daily`):
  - 7-day cycle;
  - Day 7 = 15 min of income, +400 Speed, 15 min 2× Luck;
  - **what happens on a missed day is unknown.**
- **Offline income:** `Economy.OfflineIncomeFraction` 0.5, capped at `OfflineMaxSeconds` 8 h. **Where it's shown on return is unknown.**
- **Index:** one claim per discovered pet (Speed and cash). A zone's bat skin unlocks when the zone is complete. Meadow has 7 pets.
- **Leaderboards:** Biggest Egg, Top Earners, Most Rebirths, and the BIGGEST EGG TODAY board with a 3D holder.
- **Events:** HUGE EGG every 3 resets; Capture the Egg every 20 min; Egg Golem every 30 min; Night Run every 45 min.
- **Analytics:** `OnboardingService` logs the onboarding funnel. No `LogEconomyEvent`, `LogProgressionEvent` or `LogCustomEvent` exists.

**Roblox paid random items policy** (official: https://create.roblox.com/docs/production/monetization/paid-random-items):
- Hatching an egg for a pet is a named example of a random item.
- It's "paid" when Robux, or currency bought with Robux, is spent on the random outcome. Stealing eggs is free, so free hatches need no odds.
- **Luck boosts and pity systems are named "probability modifier" items.** When one is sold for Robux:
  - its effect must be explained **numerically before purchase**;
  - the **boosted odds must be shown, updated live,** while it is active.
- Odds are shown as percentages summing to 100% (rounding is allowed, with a disclaimer).
- `PolicyService:GetPolicyInfoForPlayerAsync(player)` returns `ArePaidRandomItemsRestricted`. When it's true, the player must not buy paid random items or modifiers. The docs suggest offering a direct-purchase alternative.

## 3. Method

### Step A: audit (read-only), with parallel subagents

**Launch one subagent per area, all at once:**
1. Day 1;
2. Day 7 and retention;
3. pricing and policy;
4. RNG and pity;
5. community and social.

Give each the Part B lines for its stage, plus section 2 of this file.

**Prompt template:**

```text
You are auditing Roll and Steal an Egg! in Roblox Studio (studio_id from list_roblox_studios; place
122907972658396). READ-ONLY: no edits, no playtests, no require() of server modules (use script_read /
script_grep / inspect_instance / search_game_tree; loadstring(Source) only to compile-check).
Area: <AREA>. Checklist lines to audit (verbatim): <PASTE LINES>.
Background facts: <PASTE SECTION 2>.
For EACH line return one entry:
- Line: <text>
- Status: DONE | PARTLY | NOT BUILT | UNKNOWN
- Evidence: script/instance paths with line numbers, exact values read
- What building it would touch: files/instances, server vs client, new remotes, new Creator Hub items
- Effort: XS/S/M/L
- Needs a user decision? yes/no + the question in one sentence
Do not guess. If you could not find it, say UNKNOWN and what you searched.
```

**Example entry** (shows the format only; the code and values in it are invented, not real findings):
- Line: "VERIFY / NEW: does missing a day reset the Daily cycle?"
- Status: DONE (the reset exists)
- Evidence: `RetentionService` line 88: `if now - data.Daily.Last > 48*3600 then data.Daily.Day = 1`. The streak resets after one missed day.
- What building it would touch: `RetentionService` (server) plus `Config.Retention.Daily.GraceDays`. No new remotes and no Creator Hub items.
- Effort: XS
- Needs a user decision? Yes: "Forgive one missed day per 7-day cycle?"

**Then run a skeptic subagent** on every entry that says DONE or that drives a decision. Give it the entries and this instruction: *"Re-check each claim from the code yourself. Mark CONFIRMED or WRONG with evidence."* Fix the table before moving on.

Write the audit to `handoffs/2026-10-14-psychology-checklist-audit.md`.

### Step B: one decision message to the user

Ask everything in **one message**, each question with your recommendation. Build only what's answered.

| # | Decision | Recommendation |
|---|---|---|
| N1 | Paid Luck odds disclosure (policy, not optional while Luck15/30/60 are sold): show the odds before purchase in the Shop, and the live boosted odds while active. The decision is where it shows. | An "Odds" button on each Luck product and on the egg info card, plus a live table while the boost runs |
| N2 | Gate Luck products with `ArePaidRandomItemsRestricted` | Yes; hide or disable them with a short reason |
| N3 | A permanent 2× Luck pass in the empty 499–799 R$ band | 599 R$. The user creates the pass in Creator Hub; you wire it up once you have the id |
| N4 | An identity item at 999+ R$ (VIP tag, gold nameplate, an exclusive trail) | One VIP pass at 1,000 R$, priced round |
| N5 | The 3 R$ Starter 2× Speed: intentional loss-leader, or move it to 49 R$ with more value? | Keep it if it was intentional; ask |
| N6 | Shop order: most expensive first, with a decoy next to the main pass | Yes |
| N7 | Daily forgives one missed day (if the audit shows a reset) | Yes |
| N8 | Return screen: "While you were away: $X". Exit hint for when the 8 h cap fills | Yes |
| N9 | Index progress ("5 / 7 Meadow pets") with silhouettes of undiscovered pets | Yes |
| N10 | Server-wide announcement for rare hatches (Golden Goose and up, Golden or Rainbow variants, Colossal). Eggbert gets a far bigger moment | Yes |
| N11 | A visible pity counter per rarity (e.g. "Golden Goose guaranteed in 34 eggs"). If Luck stays paid, the counter must be included in the disclosed odds | Ask; recommend yes, Golden Goose only at first |
| N12 | Sunk-effort stats panel (eggs stolen, biggest egg, total earned, playtime) | Yes, on the existing profile or plot sign |
| N13 | Group or Discord join reward (a trail colour or 15 min 2× Luck) | Ask whether a group or Discord exists yet |
| N14 | Traps5 has product id 0: create it, or hide the item | Hide it until the user creates the product |

### Step C: build, in stage order

**Order:**
1. **Policy first:** N1, N2, and verify that Instant Hatch doesn't change the outcome.
2. Analytics gap: economy events.
3. Day 7 items: N7, N8, N9, N12.
4. Pricing items: N3, N4, N5, N6, N14.
5. RNG and pity: N10, N11, rarity colours, mutation odds found.
6. Community: N13.

**Analytics gap (no decision needed):**
- Add `AnalyticsService:LogEconomyEvent` for cash sources and sinks: wheel, pen and trail purchases, and pet income in batches.
- Add `LogProgressionEvent` for zone unlocks.
- Check the signatures in the current API reference first.
- Skip this if the plan session already built it (plan P2.4).

**For each item:**
1. Inspect the existing system and reuse it. Don't add a second clock, shop or data path.
2. Implement it server-authoritatively: the server owns purchases, odds, pity counts, rewards and Daily state.
3. **Data changes are migration-safe:** new fields get defaults, existing saves still load, and the schema version is bumped if the project uses one.
4. Run a compile check (`loadstring(Source)` via `execute_luau` in Edit mode) and a `screen_capture` for anything visual.
5. Add a `DEV_NOTES.md` entry, newest first, under `## Changelog`.
6. Tick the line in the checklist note (`- [ ]` → `- [x]`) and add `(built 2026-10-xx, <main path>)`.

**Review:** after any change that touches purchases, remotes or saved data, run a read-only reviewer subagent (the `roblox-dev:roblox-reviewer` agent if installed). Ask it to check:
- remote validation;
- receipt handling, so each product grants exactly once;
- that odds come from the server;
- DataStore safety.

Fix what it confirms.

## 4. Rules (from the user; mandatory)

- **No playtests without the user's permission.** The user playtests and reports.
- **Before anything visual,** load the user's skill `roll-egg-art-direction` (`~/.claude/skills/roll-egg-art-direction/SKILL.md`) and follow it.
- **UI and world objects are real instances** in StarterGui and Workspace; controllers only bind.
- **Never move or rebuild the map.** Inspect before editing; don't override the user's changes.
- **Never copy** from `ServerStorage.REFERENCE_PARKED`, `REFERENCE_PLEASEUSE`, `Quarantine`, `Snapshots_*`, `W1_Backups` or the Steal an Egg decomp. They are consult-only.
- **No Creator Hub.** For new passes or products, give the user a list (name, price, description, icon idea). Never click through Creator Hub or enter credentials.
- **Edit-mode `require` caches stale modules.** Verify with `loadstring(Source)` or a clone parented next to its siblings.
- **Ethics:**
  - nothing may make stopping feel impossible;
  - streaks forgive;
  - timers are honest;
  - every Robux button keeps its confirm step.

## 5. Stop points

- **After Step A:** stop and send the Step B message. Don't build before the user answers.
- **If a decision is unanswered,** skip that item and keep going.
- **When all answered items are built and ticked, finish:**
  1. Commit on `claude/stoic-lamport-jqadwi` (the checklist ticks, the audit file, a report at `handoffs/2026-10-14-psychology-checklist-report.md`). Tell the user it's ready to push.
  2. The report covers:
     - the decisions made;
     - what was built (paths);
     - compile results;
     - screenshots (Shop with odds, Index progress, rare-hatch announcement, return screen);
     - the Creator Hub list;
     - the non-code list (weekly rhythm, creators, Discord);
     - a playtest checklist for the user, with a phone pass in Device Simulator and a purchase pass on each changed product.
