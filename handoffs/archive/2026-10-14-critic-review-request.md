# Request: brutal critic review of Roll and Steal an Egg!

**Date:** 2026-10-14
**Requested by:** the user (via the cloud session that maintains the repo's knowledge vault).
**Run by:** a local Claude Code session connected to Roblox Studio through the Studio MCP server.
**Output goes to:** the GitHub repo https://github.com/apollo9360/roll-egg-notes, branch `claude/stoic-lamport-jqadwi`.
- If your local clone doesn't have that branch: `git fetch origin claude/stoic-lamport-jqadwi`, then `git checkout claude/stoic-lamport-jqadwi`.
- If the clone has no remote for that repo, ask the user before adding one.
- Commit locally; the user pushes from GitHub Desktop.

Everything you need is in this file. Other files are optional unless a step below names them with a path.

---

## 1. The goal

Produce a full, honest critic review of the game: what will make new players leave, what will stop them coming back, and what to fix first.

**Judge it from the point of view of the real audience:** kids aged about 7–12, mostly on phones, arriving from Roblox ads aimed at Simulation and Party/Casual players. **Rank problems by their effect on what Roblox's recommendations reward:**
- how many new players leave in the first minutes (bounce);
- session length (playtime);
- how many days players come back (play days);
- playing with friends (co-play);
- spending.

**Read-only.** Find and explain problems; don't fix them. The user decides what gets built.

## 2. The game in brief

Confirm everything in Studio; this is only orientation.

- **Core loop:**
  1. Walk from the lobby to a zone and grab an egg from a nest guarded by a sleeping animal guard.
  2. The guard wakes and chases.
  3. Roll the egg home along the ground. The egg **grows as you roll it**, and bigger eggs have more luck (shown as a green clover with "xN.NN").
  4. Cross the gold **SAFE ZONE** line; the guards stop there.
  5. Place the egg on your plot, wait for it to hatch into a pet, and the pet earns money per second.
- **Zones:**

  | Zone | Unlocks at | Guard |
  |---|---|---|
  | Meadow | start | Mother Hen |
  | Riverbank | 300 Speed | River Croc |
  | Canyon | 8,000 Speed | Canyon Scorpion |

- **Other systems:** Speed stat; hamster wheels (grow eggs and speed up hatching); egg variants (Plain to Rainbow); trails; Index; daily rewards; quests; rebirth; shop.
- **Tutorial:** driven by `ReplicatedStorage.Shared.Config.Tutorial`, `TutorialService` (server) and `TutorialController` (client).
- **Guaranteed first pet:** the first egg hatches an Epic Owl.
- **Standing rules from the user:**
  - Never move or rebuild the map.
  - UI and world objects are real instances in StarterGui and Workspace.
  - Never copy from `ServerStorage.REFERENCE_PARKED` (a decompiled copy of the game "Steal an Egg") or any `Quarantine`, `Snapshots_*` or `W1_Backups` folder. You may look at the decomp to describe what that genre leader does, labelled as the reference game.

## 3. Rules for this task

- **Don't change anything:** scripts, instances, configs, saved data or the map.
- **Playtests:** ask the user before starting one. If they allow it, use a solo playtest on their own save, wiped to a fresh new player with the admin tool. If not, work statically and label it.
- **Edit-mode `require` caches modules.** To read config values, read the module's `Source`, or use a fresh clone placed next to its siblings.
- **Creator Hub:** don't open it. For product prices, call `MarketplaceService:GetProductInfo(id, Enum.InfoType.Product)` (or `.GamePass`) in `execute_luau`. If you can't, write "price not checked".
- **Never run an exploit against the game.** Reason about exploits from the code.
- **Evidence labels.** Tag every important claim with one of:
  - **[Measured]:** seen in a playtest, a screenshot or the console;
  - **[Computed]:** derived from code or config, with the formula shown;
  - **[Static]:** read from code without running it;
  - **[Opinion]:** your judgement.
- **No invented data.** If the game has no analytics funnel, don't make up drop-off percentages; use the simulated timeline in step B instead. If you can't find a value, write "not found" and say where you looked.

## 4. Yardsticks

These are community benchmarks gathered from Roblox developers. Use them to judge, and cite them as "community benchmark".

- **Healthy targets:**
  - tutorial completion 75–85%; under 50% is weak, and 10–30% usually means a bug;
  - first-5-minute retention 60–65%;
  - D1 10–15% (19–20% is excellent; 3–5% means weak onboarding or core loop);
  - average playtime 15–20+ minutes for simulators; under 4 minutes means broken onboarding.
- **First 60 seconds:**
  - spawn next to the first objective, facing it;
  - no long walk before the first reward;
  - a number going up from second 1;
  - a big power jump with a full-screen reveal within about 45 s;
  - a goal visible on screen at all times.
- **Tutorial UI:**
  - about 5 words per step;
  - a bright beam or arrow that appears immediately and points at the exact target;
  - re-highlight the target after about 30 s idle;
  - hide optional UI and Robux offers during the tutorial;
  - no step a player can't complete; no softlock on rejoin;
  - UI animations around 0.2 s.
- **Early pacing:**
  - first purchase within 5–10 seconds;
  - something meaningful every 30–60 seconds early on, then every 5–10 minutes later;
  - a "$x / $y" progress ticker toward the next buy;
  - numbers should grow by roughly ×10 every 5–10 minutes in this genre.
- **Prompts:**
  - daily rewards, invites and group prompts never during the tutorial;
  - invites after real wins, not on a timer from second 0.
- **Phone UI:**
  - touch targets of at least 44 pt, text of at least 14 pt;
  - no Robux buttons in the thumb zones (bottom-left joystick area, bottom-right action area);
  - about 10 always-on HUD elements is already too many.
- **Honesty:**
  - shop text must be literally true;
  - no fake "was" prices or fake "limited" labels;
  - no fake players or bots disguised as players.
  These are trust and moderation risks in a kids' game.

## 5. How to do it

Work in four steps, A to D. Steps B and C use **subagents**: in Claude Code, launch them with the Agent (Task) tool.
- Before starting, check whether a subagent can use the Roblox Studio MCP tools: launch one test subagent that calls `list_roblox_studios` and reports the result.
- **If subagents can't reach Studio:** step A must capture everything they need into files, and the subagents work from those files plus reading the repo. Otherwise, give them Studio access with the `studio_id`.

### Step A: build the fact base (you, in the main session)

Write `handoffs/critic-review-facts/` (one Markdown file per topic). The reviewers all work from this, so their numbers agree.

1. `configs.md`: every ModuleScript under `ReplicatedStorage.Shared.Config`, with its full source.
2. `services.md`: every server service and client controller (paths), with 1–2 lines each on what it does, and the remotes each one handles.
3. `remotes.md`: every RemoteEvent and RemoteFunction: path, who fires it, what the server checks (types, ranges, ownership, rate limit), and what it changes.
4. `products.md`: every developer product and game pass id the code references, its price (via GetProductInfo), and where in the UI it is offered.
5. `tutorial.md`: every tutorial step: id, card text, target, completion condition, rewards, and when onboarding is saved as done.
6. `world.md`: spawn position and facing; distances from spawn to the Meadow nest, safe line, plot, shop and wheel; Workspace part count; instance count; StreamingEnabled; lights and shadows.
7. `ui.md`: every ScreenGui element visible at spawn, with size and position on a small phone (use Device Simulator if a playtest is allowed).
8. `screenshots/`: spawn view, tutorial step 1, the guard chase, the safe line, the plot, the hatch reveal, the shop, and the HUD on a phone. Use `screen_capture`; edit-mode captures are fine if there's no playtest.

### Step B: run 12 critic subagents in parallel

Launch one subagent per area below, all at once. Give each one the **prompt template** with its area filled in.

| # | Area | What to examine |
|---|---|---|
| 1 | First 60 seconds and tutorial | Spawn framing, the walk, guidance, UI at second 0, popups, whether each step needs the player's own action, when onboarding is saved, rejoin behaviour |
| 2 | Core loop: steal, roll, escape | Grab feel, guard wake and chase, danger curve, the safe line, whether rolling *feels* like growth, sound, shake and numbers, what happens on a failed first run |
| 3 | Egg growth, luck and the hatch | Growth rate, caps, variants, clover readability; odds of each rarity in the first 10 minutes (computed from drop tables and the luck formula); how strong the hatch reveal is; whether bigger egg = better pet is visible |
| 4 | Economy and pacing | Simulated income and purchase timeline (see the timeline table below); dead zones; purchases that change nothing visible; number growth speed |
| 5 | Progression systems | Speed sources, wheel tiers, zone unlocks, Index rewards, rebirth, trails. Does each feed the core loop? Is there always a visible next goal? |
| 6 | Zones, guards and the world as a challenge | Variety and difficulty of each zone, guard readability, night, the 5-minute egg reset, whether zone 2 feels new |
| 7 | Monetisation | A catalogue table; pricing and spending ceiling; missing passes or Starter Pack; prompts during the tutorial; misleading copy; paid boosts lost on rejoin or overwritten |
| 8 | Retention and return loops | The kid's Day 1 / Day 2 / Day 7 story; daily rewards (paid by server or client? calendar days or rolling?); quests; offline earnings; favourite prompt; notifications; scheduled events |
| 9 | Social and co-play | Can others ruin a newcomer's game? Do friends get a visible payoff? Invites, show-off moments, any bots |
| 10 | HUD, UI clarity and mobile | A phone screen map, touch targets, text sizes, overlaps, thumb zones, wording consistency, fonts, a next-goal chip |
| 11 | World, first impression and performance | Frame by frame from join; part and instance counts; per-frame scripts; peak VFX counts; mobile quality scaling |
| 12 | Trust and security | Remotes that trust the client (money, Speed, egg size, placement, daily claims); dupes on rejoin; races while carrying or placing eggs |

**The economy coach (4) builds this timeline.** Simulate a new player who always does the obvious next thing, from the configs:

| Time played | Income per second | Next purchase and price | Wait |
|---|---|---|---|
| Min 1 | | | |
| Min 5 | | | |
| Min 15 | | | |
| Min 30 | | | |
| Hour 1 | | | |
| Hour 2 | | | |

Coach 1 builds the same kind of table for the first 10 minutes: time to the first steal, first safe line, first hatch, first purchase, first wheel, Riverbank unlock and first rare pet.

**Prompt template for each critic subagent:**

```text
You are critic #<N> of 12 reviewing the Roblox game "Roll and Steal an Egg!". Your area:
<AREA NAME> — <WHAT TO EXAMINE, copied from the table>.

Audience: kids 7-12, mostly on phones, arriving from ads. Rank problems by their effect on
bounce, playtime, return days, playing with friends, and spending.

Read first: <absolute path to handoffs/critic-review-facts/> (all files) and the yardsticks
below. <If you have Studio MCP access: studio_id = ...; read-only, never edit, never start
or stop a playtest.> <Paste section 4 "Yardsticks" here.>

Be independent and blunt. Every claim needs evidence: an instance or script path with a line
number, a config value, or a formula. Label claims [Measured], [Computed], [Static] or
[Opinion]. Never invent numbers; write "not found" if you can't find something.

Return Markdown in exactly this shape:
## <N>. <Area> (<score>/10)
<2-sentence verdict>
### The kid's experience
| Time | What happens | Risk |   (or a Day 1 / Day 2 / Day 7 story for retention)
### Problems, ranked
1. **<one-line problem>.** <evidence>.
   - Fix: <concrete change with numbers>. Effort XS/S/M/L · Impact Low/Med/High/Very high.
(5-10 problems, most important first)
### What's good
<2-4 sentences of genuine strengths, with evidence>
### Top 3 for the summary
<your three highest-impact fixes, one line each>
```

**Example of one problem entry,** at the expected level of detail (made up; not about this game):

```text
1. **The first big reward arrives at 5–20 minutes.** [Computed] Boss HP 8,000,000
   (Config.Boss.Hp, line 12) ÷ a new player's ~110 DPS ≈ 20 h solo; with 12 players ≈ 61 min.
   - Fix: give new players a personal starter boss with 1,500 HP, 25 studs from spawn, that
     breaks in ~15 s and pays 500 coins. Effort S · Impact Very high.
```

### Step C: verify the strongest claims with a skeptic subagent

Collect every coach's "Top 3" (up to 36 items). Launch **one skeptic subagent** with this prompt:

```text
Here are claims from 12 critics of a Roblox game, each with its evidence. For each claim,
re-check the evidence against the facts in <path to critic-review-facts> (and Studio if you
have access). Mark it CONFIRMED, WEAKENED (explain), or WRONG (explain). Don't add new
findings. Return a table: Claim | Verdict | Why.
```

Drop or correct anything marked WRONG, and soften anything WEAKENED.

### Step D: write the report (you, in the main session)

Write `handoffs/2026-10-14-critic-review.md` in this order:

1. **TL;DR: the 10 fixes that matter most.**
   - One paragraph naming the single biggest diagnosis (where the coaches agree), and the average score.
   - Then 10 numbered fixes, ranked by impact on bounce, playtime, play days, co-play and spend. Each is one bold sentence plus its key number.
2. **What the data says.** The funnel if one exists, otherwise coach 1's 10-minute timeline. Name the moments where players are most likely to leave, and why.
3. **The 12 coach sections,** as returned, after step C's corrections.
4. **Master action plan,** as checkbox lists ordered by impact ÷ effort:
   - **This week:** quick wins, mostly under an hour each;
   - **Next 2 weeks:** the core rebuild;
   - **Later:** depth for days 7–30.
   The first item is always analytics: the onboarding funnel steps to log with `AnalyticsService:LogOnboardingFunnelStepEvent`, so every fix can be measured.
5. **Method and limits.** What was playtested versus static; what couldn't be checked.

## 6. Finish

1. Commit `handoffs/2026-10-14-critic-review.md` and `handoffs/critic-review-facts/` on `claude/stoic-lamport-jqadwi`.
2. Tell the user it's ready to push from GitHub Desktop, and give them the TL;DR in chat.
3. Don't start any fixes.
