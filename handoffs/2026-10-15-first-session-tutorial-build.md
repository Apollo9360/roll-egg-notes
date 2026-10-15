# Build request: first session and tutorial tightening (Roll a Giant Egg)

**Date:** 2026-10-15
**Written by:** the cloud session. It can't reach Studio.
**Built by:** a local Claude Code session connected to Roblox Studio (place 122907972658396). You are the **director**: you plan, run subagents, check their work, and own the result.
**Repo:** https://github.com/apollo9360/roll-egg-notes, branch `claude/stoic-lamport-jqadwi`.
- Get it with `git fetch origin claude/stoic-lamport-jqadwi`, then `git checkout claude/stoic-lamport-jqadwi`, then `git pull`.
- Commit locally; the user pushes from GitHub Desktop.

**Paste line for the user:** `Read handoffs/2026-10-15-first-session-tutorial-build.md on branch claude/stoic-lamport-jqadwi (git fetch first) and do what it says.`

**The game is now called "ROLL A GIANT EGG"** (renamed from "Roll and Steal an Egg!"). Older notes and reports use the old name.

**Order with the other open handoffs:**
- `handoffs/2026-10-15-retention-monetization-build.md` (freebie egg, playtime rewards, starter packs, hit popup, leave overlay)
- `handoffs/2026-10-15-satchel-chase-egg-return-build.md` (Satchel, chase feel, guards carrying loose eggs home)

**Don't run two handoffs in the same place at the same time:** two directors editing one place overwrite each other. If either is unfinished, ask the user which to run first. Overlaps:
- **Feature 4 (nest respawn)** touches the same nest API as the Satchel handoff's feature 3 (a guard re-nests a loose egg). Whichever runs second must reuse the first one's code, not add a second path.
- **Feature 5** hides Robux prompts during the tutorial. The other handoff's starter packs and hit popup must follow the same rule.
- **Feature 2** changes the tutorial step list. The other handoffs' tutorial freebie egg reads tutorial state, so re-check it after this lands.

---

## 0. Why this handoff exists

The user's live data (2026-10-15):

| Source | Reading |
|---|---|
| Game page | 200 visits, 6 favourites, 4 likes, **Ages 16+** (not yet all-ages), server size 5 |
| Ads, campaign 1 (Oct 13–14) | 23,425 impressions, 1.7% CTR, 405 clicks, **86 plays (21% of clicks)**, 0.099 credits per play |
| Ads, campaign 2 | about 2.6% CTR after 24 h; a snake + golden egg thumbnail at 4.4% |
| Onboarding funnel | 26 users, **23% reach Done**; 19% never leave spawn; biggest single drop at Tut10_River2 (2 of 9) |

**Read the funnel with care.** Only 26 users, and Roblox analytics lag 1–2 days. The funnel was also **renumbered three times** (original, a 13-step version, then today's 16-step version). Roblox files funnel events by step number and shows the latest name, so older players are counted under today's names. Most of the low click-to-play rate is probably the 16+ age gate. The tutorial changes here are therefore judged on fresh data **after** they ship (feature 1), not justified by this funnel.

**What Hud's Guide says** (in this repo: `huds-guide/08 Retention, Onboarding and Playtime/`):
- Tutorial completion: 75–85% is the target, 50–70% the common real range, under 50% weak.
- **Length alone costs completion.** At 95% per step, a 5-step tutorial completes about 77% and a 15-step one about 46%.
- Keep the tutorial to the core loop and introduce the rest in context.
- Fix the first 30 seconds before anything later.
- Spawn players next to the first objective, facing it.
- About 75–80% of players are on mobile.

## 1. What is already built (do NOT redo; verify it still works)

From the 2026-10-14 build reports (first-10-minutes, feel pass, galaxy egg, psychology checklist):

**Tutorial is `Config.Tutorial` v3** with 13 steps and Done = 13:

| # | Step | Notes |
|---|---|---|
| 1 | Steal | "Hold {Key} on an egg at the Meadow nest." `{Key}` = E, X or "Tap and hold" |
| 2 | Escape | "Cross the gold line. Your egg grows as it rolls!" |
| 3 | Home | |
| 4 | Place | |
| 5 | Hatch | |
| 6 | Buy | "Click/Tap Buy below." (the NextGoal chip) |
| 7 | Wheel | |
| 8 | Index | Claim required |
| 9 | River | |
| 10 | River2 | |
| 11 | FreeSkip | |
| 12 | Trail | Green Trail, $5,000 |

- **Migration:** a `TutorialVersion` migration exists (v1 → v2 → v3), and the old LegacyDone collisions were removed.
- **Free skip:** the delivered Riverbank egg gets a 15:00 timer and a one-time free skip (`FreeSkipUsed`).
- **Completion reward:** 10 min of 2× Luck plus the `StarterGui.TutorialComplete` card, then a protection toast.
- **Stuck-proofing** exists for Hatch with no egg and no pet, and for FreeSkip.

**Funnel:** `OnboardingService` funnel v3 has 16 steps:

| # | Step | # | Step |
|---|---|---|---|
| 1 | Joined | 9 | Tut6_Buy |
| 2 | LeftSpawn | 10 | Tut7_Wheel |
| 3 | EnteredMeadow | 11 | Tut8_Index |
| 4 | ReachedNest | 12 | Tut9_River |
| 5 | Tut2_Escape | 13 | Tut10_River2 |
| 6 | Tut3_Home | 14 | Tut11_FreeSkip |
| 7 | Tut4_Place | 15 | Tut12_Trail |
| 8 | Tut5_Hatch | 16 | Tut13_Done |

- Renumbered on 2026-10-14.
- `LeftSpawn` is measured from the player's own plot Spawn.
- `funnel.New` counts only brand-new saves.

**First session:**
- The **Owl is auto-placed** in the pen.
- **Progressive HUD reveal:**
  - from spawn: Stats and the tutorial card;
  - first placed egg: Eggs tab and Timer;
  - first hatch: Pets tab;
  - step 8: Index;
  - Done: everything else (Shop, Daily, Quests, Rebirth, Settings, Stats, group card).
- `Hud.NextGoal` chip ladder: Wheel $100 → Pen 2 → Wheel 2 → Pen 3 → Green Trail → … → next zone.
- PopBanner (BIG / HUGE / COLOSSAL), the "SAFE!" burst, and the `ZoneUnlockController` "RIVERBANK UNLOCKED!" banner.
- PvP and night hints are gated until Done.
- The phone tutorial card is 340×85.
- The favourite prompt shows after the first Legendary+ hatch or at 9 minutes.
- `DailyAutoOpen` fires on the next join only.

**Nests and plots:**
- A **personal starter egg** for players on the Steal (Meadow) or River (Riverbank) step, within 40 studs of an empty nest, once per refill.
- Night returns non-tutorial carried eggs to their nest slot; tutorial players are exempt from night.
- A plot **wait list plus `TeleportAsync`** for a 6th player. The user has set **server size 5**.
- `FirstWalkCollected` stops re-farming the first-walk pickups.

**Guards:** first-timer grace (wake 1.3 s, no hop, skid stop) on the tutorial steps, one escape per zone.

**Other systems:**
- odds disclosure;
- the `PolicyService` gate on Luck;
- a rolling 20 h Daily;
- the offline return card;
- the Stats panel;
- rare-hatch announcements;
- the 22 h group chest;
- the Galaxy Egg;
- the permanent Speed chain.

**User decisions already made:**
- **N11: no pity system.**
- N5: the 3 R$ starter unchanged.
- D2/D3: Riverbank is in the tutorial; the Daily and Protection steps were dropped.
- D4: the completion reward.
- D6: night exempts tutorial players.
- D7: leave the EggStatue.

## 2. The critic review (read before planning)

A full critic review ran on 2026-10-14, **before** the first-10-minutes build landed, so many of its findings are already fixed (section 1). Copies are in this repo:

| File | What |
|---|---|
| `handoffs/2026-10-15-critic/TOP3.md` | Each of the 12 areas' top 3 fixes, with scores |
| `handoffs/2026-10-15-critic/parts/01.md` … `12.md` | Full findings per area: 01 first 60 s, 02 core loop, 03 growth and hatch, 04 economy, 05 progression, 06 zones, 07 monetisation, 08 retention, 09 social, 10 HUD and mobile, 11 world and performance, 12 trust and security |
| `handoffs/2026-10-15-critic/parts/SKEPTIC.md` | Re-check of 36 items: 50 rows confirmed, 3 weakened, 0 wrong. Corrects the Owl income: about $75–90/s, not $24–40/s |
| `handoffs/2026-10-15-critic/facts/*.md` | Read-only code snapshots: tutorial, ui, world, services |

- **The snapshots predate v3.** They describe the old 16-step tutorial, so their line numbers and step numbers are stale. Use them to know where to look, never as truth.
- **The copies were decoded by hand from the user's Drive folder** (https://drive.google.com/drive/folders/17CUp1r-gpdRPuMyIJOQ7eyhf2tl_QchT), so a character may be off. The Drive files are the originals.

## 3. The features

Every feature starts with **"verify it's still open"**. If the audit shows it's already done, mark it done and move on.

### Feature 1: Funnel hygiene (do this first)

**Why:** the funnel is the only way to judge features 2 and 3, and today it mixes three versions.

1. **Trace the funnel events on a wiped save** (needs the user's OK for a solo playtest).
   - Each of the 16 events must fire once, in order, for a new save.
   - Returning saves must fire none.
2. **Versioning rule for the future**, documented in `DEV_NOTES.md` and in a header comment in `OnboardingService`:
   - Never renumber or rename a live funnel step.
   - When the step list changes (feature 2 will), ship it as a **new, dated version** in one publish.
   - Record the publish date and time in `DEV_NOTES.md` so the user filters the Funnels page to dates after it.
3. **Add a cheap leave-reason signal for spawn bounces.** For example, a custom analytics event or a field on the existing logs: `SecondsInSession` and `TutorialStep` on `PlayerRemoving` for players who never reach `LeftSpawn`. This helps tell bots and AFK players from confused players. Keep it light; no new remotes.
4. **Report** the exact funnel step list after feature 2, so the user knows what the new version's rows mean.

### Feature 2: Shorter tutorial (needs a decision, Q1)

**Why:** 12 steps after joining is long. The guide's compounding math puts completion near 50% even if every step holds 95%. The core loop is done by step 5. The critic (part 05 #4) also found the tutorial ends on the **worst-value purchase**: the Green Trail at $5,000 gives +1 Speed/s, while Wheel 2 at $1,500 gives +4/s.

**Proposal (recommend; the user decides in Q1):**
- **Remove step 12 Trail.** The tutorial ends when the FreeSkip egg hatches (step 11). The completion reward and card fire there. The `NextGoal` chip already points at the next buy, so the Trail stays reachable.
- **Keep** 1–5 (core loop), 6 Buy, 7 Wheel, 8 Index (it pays +320 Speed, which opens Riverbank), 9–10 River and 11 FreeSkip.
- **Optional (Q2):** fold step 8 into the first hatch moment, e.g. the Owl hatch card offers "Owl discovered! Claim +320 Speed". Ask before doing this; it changes the Index teaching.
- **Shortening is cheaper than adding:** fewer steps, the same systems. Don't add new steps.

**Must handle:**
- **A `TutorialVersion` v3 → v4 migration.** Saves on the removed step go to Done, with the completion reward granted once. No save may get stuck or get the reward twice.
- **Funnel:** ship a new dated funnel version in the same publish (feature 1).
- **Every reader of the step list:**
  - `TutorialService` / `TutorialController`;
  - `HudController`'s reveal gates (Done reveals the side bar);
  - `OnboardingService`;
  - hints and quests gated on `Tutorial.InProgress`;
  - the FreeSkip code;
  - `GuardService` grace;
  - the night exemption;
  - the personal starter egg;
  - the other handoffs' features, if built.
- The card's step counter, if shown, must show the new total, or better, no `n/N` counter at all (the critic recommends removing it).

### Feature 3: The first 10 seconds (LeftSpawn loses 19%)

**Why:** 5 of 26 players never left spawn. Causes could be loading, AFK or bot accounts from Plays ads, or confusion. The critic found three concrete problems (parts 01 #7, 11 #3 and #10). Verify each against today's build first.

1. **Spawn snap.**
   - Today the engine spawns everyone at `Workspace.World.Lobby.LobbySpawn` (facing west, the only SpawnLocation). Then `PlotService.spawnAtPlot` waits a frame plus `RequestStreamAroundAsync`, and teleports the character about 15 studs onto the plot porch. This repeats on every respawn.
   - **Fix (Q3):** make each plot's `Spawn` part a neutral `SpawnLocation` and set `player.RespawnLocation` when the plot is assigned. Keep the `spawnAtPlot` PivotTo as a fallback only.
   - Keep the plot's facing.
   - Check the wait-list, teleport and admin-wipe paths.
2. **What the camera sees first.**
   - Today the "HUGE EGG SOON" podium and "BIGGEST EGG TODAY" board sit centre-frame. The nest is 179 studs away and off screen. The beam is 1.6 studs wide.
   - **Fix (Q4):** for players with `TutorialStep <= 2`, make those props **locally** invisible (`LocalTransparencyModifier`; client-side only, nothing moved).
   - Turn the spawn heading so the first view includes the Meadow gate and chevrons. If that needs moving a part, ask: map rule.
   - Make the beam wider or brighter on step 1, as a `Config.Tutorial` value.
3. **Loading.**
   - `ReplicatedFirst` is empty and there are no `PreloadAsync` calls.
   - **Fix:** a small ReplicatedFirst loader that holds a simple branded screen until `DataController` is ready and the player's plot has streamed, **capped at 6 s**. Preload the HUD icons.
   - Use the game's art direction (load the `roll-egg-art-direction` skill first).
4. **Streaming (Q7).**
   - The critic proposes `StreamingMinRadius` 96 and `StreamingTargetRadius` 350. Lobby, plots and nest are all within about 215 studs.
   - Read the current values first; the skeptic could not read them over MCP.
   - Change them only with the user's OK.

Leave the zone `EntranceSign`s alone unless the user answers Q5. They sit at Y = −84, but the skeptic found they were moved down 100 studs by a manual edit of unknown intent; backups have them at Y = +16.

### Feature 4: Nests that don't run dry (for everyone, not just the tutorial)

**Why:** nests refill only every 300 s (at day start, plus Night Run). That's 5 eggs per zone per 5 minutes, shared by the whole server, and a delivered egg's nest stays empty. The personal starter egg already covers tutorial players near an empty nest. After the tutorial, a new player in a 5-player server can still wait minutes. Critic parts 02 #1, 04 #1, 06 #8, 09 #2.

1. **Respawn each taken nest egg after a delay**, set per biome in config (Q6). Recommend Meadow 20 s, Riverbank 30 s, Canyon 45 s.
   - Rare and HUGE spawns, Night Run and the day-start refill keep working.
   - A respawn never replaces an egg someone is carrying, and never orphans one.
2. **Show it.**
   - When a nest is empty, its billboard shows "Next egg in 0:18".
   - When the player is near an empty nest ring, the step-1 distance pill and the hint say the same, instead of "Hold E on an egg!".
3. **A night grab says why it failed.** Today `grabEgg` returns silently at night. Show "Night! The nests sleep for a few seconds."
4. **Coordinate with the Satchel handoff's feature 3** (a guard carries a loose egg back to its nest): one nest API, one place that decides a slot is free.

### Feature 5: Tutorial safety and fairness gates (small items, each verify-first)

| Item | Problem (critic part) | Fix |
|---|---|---|
| a | Robux **Skip** prompt shows on tutorial eggs (07 #4) | Hide paid Skip while `Tutorial.InProgress`. The FreeSkip prompt stays |
| b | **Trail Stand RobuxPrompt** always enabled (07 #4) | Enabled only when not owned **and** the tutorial is done |
| c | Skip is a 0.25 s hold next to Hatch and Roll, and buys at once (07 #8) | `SkipHold` 0.8 s; hide when under 20 s left; route through the existing `ConfirmProduct` confirm (already required by the user's rule) |
| d | Capture and Golem **event banners** reach tutorial players; the arena `canHit` ignores protection (09 #6) | Hide unrelated announce banners while in the tutorial; treat tutorial and protected players as outside the arena |
| e | **Protection** runs on wall-clock time, so Day 2 starts unprotected (08 #6, 12 #5) | Count protection in **online seconds** (`ProtectedLeft`) and warn "Shield ends in 60 s" (Q8) |
| f | Day-1 **quests** can be impossible (KnockLoose needs a bat; protected players can't bat) (08 #7) | Until Done + 3 days, draw only Deliver, Hatch, RollKg, Escape |
| g | **One-tap Sell** in the Pets list; a double-tap sells the next pet (12 #2) | Two-step "Tap again to sell", 0.8 s lockout, protect placed and top pets |
| h | **Keyboard letters on touch** in step texts | All step texts use `{Key}`-style tokens; check every step on a phone profile |
| i | **Failure explains nothing** (02 #4) | Cause-specific catch toast ("The hen was faster! Run straight to the gold line"); at a zone gate, "River Croc is too fast! Need Speed 300 (you: N)" |

### Feature 6: The ROLLGIANT code

The game page now says: "🎁 CODE: ROLLGIANT for a free boost! (Codes button on the left)".

- **Codes exist:** `Codes.lua` has LAUNCH, EGGROLL and THANKYOU, per the critic; Codes.lua is in ReplicatedStorage.
- **Not found by the critic:** ROLLGIANT, and any Codes button. The left HUD bar holds Shop, Daily, Quests and Rebirth.

1. **Find how codes are redeemed today** (Settings? a menu?). Tell the user exactly where, so the page text can be corrected.
2. **Add ROLLGIANT (Q9).** Recommend 15 min of 2× Speed: not a random item, so no odds rules apply. One claim per account, through the existing code path.
3. **Move the code list server-side** (`ServerStorage`), so unreleased codes aren't readable by clients (critic 12 #10). The client only sends the typed string.
4. If there's no visible way to enter codes, propose one (a small "Codes" entry in Settings or the side bar after Done) in the Step B message rather than inventing it.

**Out of scope here** (other handoffs or the user's later call):
- chase feel and hen speed (Satchel handoff);
- starter packs, playtime rewards, the freebie egg, the leave overlay (retention handoff);
- the late-game economy rescale (critic 04 #3–#6);
- zone mechanics (06);
- performance passes (11 #1–#2, except Q7);
- the movement anti-cheat (12 #3);
- pity floors: **the user said no pity (N11)**, so don't add the critic's "Epic-leaning floor" for the first eggs.

## 4. Method (director)

### Step A: fact base (read-only), in parallel

Launch **four audit subagents at once**:
1. features 1 + 2;
2. feature 3;
3. feature 4;
4. features 5 + 6.

They only read: `script_read` / `script_grep` / `search_game_tree` / `inspect_instance`, and `loadstring(Source)` for compile checks.

**Prompt template:**

```text
You are auditing ROLL A GIANT EGG in Roblox Studio for planned work. READ-ONLY: no edits, no playtests,
no require() of server modules. Studio: list_roblox_studios → place 122907972658396.
Work: <PASTE THE FEATURE SECTION(S)>.
Already built (verify, don't redo): <PASTE SECTION 1>.
Critic evidence (stale line numbers; find the current code): <PASTE THE MATCHING ROWS FROM
handoffs/2026-10-15-critic/parts/*.md>.
Return:
1. For each item: STILL OPEN / ALREADY DONE / PARTLY DONE, with paths, current line numbers and quoted lines.
2. Every system the change touches or could break (tutorial migration, funnel, HUD reveal, night,
   starter egg, guard grace, FreeSkip, other handoffs' features).
3. Streaming, performance and security risks.
4. Questions only the user can answer, each with your recommendation.
5. A build plan: ordered steps, files, server vs client, effort XS–L.
Say "not found" with where you looked rather than guessing.
```

**Then run one skeptic subagent over the four audits:** "Re-check every path, line number and claim from the code yourself. Mark each CONFIRMED or WRONG, with evidence." Write the fixed fact base to `handoffs/2026-10-15-first-session-audit.md`.

### Step B: one decision message to the user

Include a "already done" list from the audit, then:

| # | Question | Recommendation |
|---|---|---|
| Q1 | Remove the Trail step, so the tutorial ends at the FreeSkip hatch? | Yes |
| Q2 | Fold the Index claim into the first hatch card? | No for now; one change at a time |
| Q3 | Spawn on per-plot SpawnLocations with `RespawnLocation` instead of the teleport snap? | Yes |
| Q4 | Hide the podium egg and Biggest Egg board locally until the first steal; turn the spawn heading toward the Meadow gate? | Yes (client-only hiding; ask before moving any part) |
| Q5 | Raise the six zone EntranceSigns back to Y = +16 (backups have them there)? | User's call: they were moved down by hand |
| Q6 | Nest respawn delays per zone | Meadow 20 s, Riverbank 30 s, Canyon 45 s |
| Q7 | Set streaming radii 96 / 350 (after reading current values)? | Yes |
| Q8 | Protection in online seconds; how long? | 900 s online, warn at 60 s left |
| Q9 | ROLLGIANT reward | 15 min 2× Speed, once per account |
| Q10 | Ship in two publishes a few days apart? (A: features 1, 4, 5, 6; B: features 2, 3 and the new funnel version) | Yes, so the user can tell what moved the numbers |

### Step C: build

**Order:** publish A (features 1 → 4 → 5 → 6), then publish B (feature 3, then feature 2 plus the funnel version). Tell the user when each batch is ready; **the user publishes**, not you.

**For each feature:**
1. Reuse what the audit found. No second nest API, second tutorial state, second HUD hide path or second code path.
2. **Server-authoritative:** the server owns tutorial steps, nest eggs, protection, codes and purchases.
3. Put every tuning number in config.
4. Run a compile check (`loadstring(Source)` via `execute_luau` in Edit mode). Take a `screen_capture` for anything visual, on a phone profile (667×375) too.
5. **Run critic subagents in parallel on each finished batch:**
   - **First-session critic:** walk a brand-new save through every step on paper. Report any path where a player is stuck, gets a reward twice, sees a Robux prompt during the tutorial, or loses an egg.
   - **UI and mobile critic:** UI and Mobile UI checklists, screenshots at 667×375.
   - **Security and performance critic:** remotes, rate limits, codes, loop rates, connection cleanup.
   Fix what they confirm.
6. Add a `DEV_NOTES.md` entry, newest first, under `## Changelog`. Include the funnel version and its date (feature 1).

**Subagent reading** (give each the ones for its job):

| Note | URL | Who |
|---|---|---|
| Player psychology checklist | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/27%20-%20Checklists/Roblox%20Player%20Psychology%20Checklist.md | features 2, 3, 5 |
| UI checklist | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/27%20-%20Checklists/UI%20Checklist.md | features 3, 4, 5 |
| Mobile UI checklist | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/27%20-%20Checklists/Mobile%20UI%20Checklist.md | features 3, 4, 5 |
| StreamingEnabled and large worlds | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/17%20-%20Performance/StreamingEnabled%20and%20Large%20Worlds.md | feature 3 |
| Hud's Guide, tutorial fundamentals | https://github.com/Apollo9360/roll-egg-notes/tree/main/huds-guide/08%20Retention%2C%20Onboarding%20and%20Playtime | features 1, 2, 3 |

## 5. Rules (from the user; mandatory)

- **NEVER download or install anything as a skill** (or plugin) unless the user explicitly said so beforehand.
- **No playtests without the user's permission.** Ask in the Step B message for one solo playtest on a wiped save per publish batch. Restore the user's save afterwards, as the earlier passes did.
- **Before anything visual,** load the user's installed skill `roll-egg-art-direction` (`~/.claude/skills/roll-egg-art-direction/SKILL.md`) and follow it. Every new UI uses the `Celebrate` effects (the standing rule in DEV_NOTES).
- **Never move or rebuild the map.** Inspect before editing; don't override the user's changes. Client-side `LocalTransparencyModifier` is fine; moving parts needs a yes.
- **Reference folders** (`REFERENCE_*`, `Quarantine`, `Snapshots_*`, `W1_Backups`, the decomp): read them freely. Use their image, sound and mesh assets only after the open-use check in `handoffs/2026-10-15-retention-monetization-build.md` (section 3, Step A0). Never copy their scripts, UI layouts or models.
- **No Creator Hub.** Never click through it or enter credentials. Publishing and product ids are the user's job.
- **Edit-mode `require` caches stale modules.** Verify with `loadstring(Source)` or a clone parented next to its siblings. Never `require` `Shared.Net` a second time on the server.
- **UI is real instances in StarterGui; controllers only bind.**
- **Every paid prompt goes through the in-game confirm.** No Robux prompt during the tutorial.

## 6. Stop points and finish

1. **After Step A:** send the Step B message and wait. Build only answered items; skip anything unanswered and keep going.
2. **After publish batch A is built:** stop and tell the user it's ready to publish. Start batch B only when the user says so; they may want a few days of data first.
3. **When done:**
   - Commit on `claude/stoic-lamport-jqadwi`:
     - the audit file;
     - a report at `handoffs/2026-10-15-first-session-report.md`;
     - screenshots in `handoffs/2026-10-15-refs/built-first-session/`.
   - Tell the user it's ready to push.
4. **The report covers:**
   - the decisions made;
   - what was already done (from the audit);
   - what was built (paths);
   - the new tutorial step list;
   - the new funnel version, its step list and the date to filter from;
   - the migration rules;
   - the nest respawn config;
   - where codes are entered (so the game page text can be fixed);
   - compile results;
   - critic findings and fixes;
   - a playtest checklist for the user, covering:
     - a brand-new save from spawn to Done, on PC and in Device Simulator at 667×375;
     - a save on each old tutorial step after the migration;
     - an empty Meadow nest (wait for the respawn timer);
     - a night grab;
     - no Robux prompt anywhere before Done;
     - protection carrying over a rejoin;
     - redeeming ROLLGIANT twice (second time refused);
     - selling a pet (two taps).
