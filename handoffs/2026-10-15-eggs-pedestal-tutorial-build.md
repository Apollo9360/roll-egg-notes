# Build request: new egg models, egg-opening visuals, the Biggest Egg pedestal, a shorter tutorial (Roll a Giant Egg)

**Date:** 2026-10-15
**Written by:** the cloud session. It can't reach Studio.
**Built by:** a local Claude Code session connected to Roblox Studio (place 122907972658396). You are the **director**: you plan, run subagents, check their work, and own the result.
**Repo:** https://github.com/apollo9360/roll-egg-notes, branch `claude/stoic-lamport-jqadwi`.
- Get it with `git fetch origin claude/stoic-lamport-jqadwi`, then `git checkout claude/stoic-lamport-jqadwi`, then `git pull`.
- Commit locally; the user pushes from GitHub Desktop.

**Paste line for the user:** `Read handoffs/2026-10-15-eggs-pedestal-tutorial-build.md on branch claude/stoic-lamport-jqadwi (git fetch first) and do what it says.`

**The game is now called "ROLL A GIANT EGG"** (renamed from "Roll and Steal an Egg!"). Older notes and reports use the old name.

**Order with the other open handoffs:**
- `handoffs/2026-10-15-retention-monetization-build.md` (freebie egg, playtime rewards, starter packs, hit popup, leave overlay)
- `handoffs/2026-10-15-satchel-chase-egg-return-build.md` (Satchel, chase feel, guards carrying loose eggs home)

**Don't run two handoffs in the same place at the same time:** two directors editing one place overwrite each other. If either is unfinished, ask the user which to run first. Overlaps:
- **Feature 1 (new egg models)** changes what every egg looks like. The other handoffs' freebie egg and the guard carrying a loose egg must use the new models. Whichever runs second reuses the first one's egg-visual code.
- **Feature 2 (opening visuals)** must hide the HUD and hotbar through the Satchel handoff's single hide switch if that exists. If not, use today's hide path; never add a second one.

**Not in scope:** the 2026-10-14 critic review. The user has already implemented its findings, so don't re-audit or rebuild them.

---

## 0. Facts about the game (from the 2026-10-14 build reports; re-check each in Studio)

**Eggs today:**
- **Nest eggs:** zone eggs (Meadow cream with yellow spots, Riverbank blue with white waves, Canyon terracotta, and the later zones).
- **Variants** (`EggVariants`: Plain, Speckled, Spotted, Marbled, Shiny, Golden, Rainbow). Shiny and up glow.
- **The egg grows as it's rolled.** Size drives diameter through a log scale, plus weight and luck.
- **Other egg kinds:**
  - HUGE and rare spawns;
  - the starter egg;
  - the tutorial FreeSkip egg;
  - the **Galaxy Egg** (`Assets.GalaxyEgg`, shown in the Shop banner and `GalaxyOpenController`).
- **Where eggs are drawn:**
  - nests (`EggService`);
  - carried and rolling (`CarryController`, `EggFxController`);
  - placed and incubating on plots (`PlotEggController`, `IncubatingEggs`);
  - the hamster wheel (`WheelFxController` scales the wheel to fit a big egg);
  - the hatch animation (`PlotEggController.playHatch`);
  - the Galaxy opening (`GalaxyOpenController`);
  - the lobby `EggStatue` (**the user said leave the EggStatue alone**);
  - the Biggest Egg pedestal and board (`PlazaService`).
- **Hatch reveal today:**
  - `HudController.hatchReveal` shows a HatchCard (Rarity, PetName, Stats).
  - Epic+ gets a 4.5 s full-screen reveal (sunburst, "EPIC!", confetti, camera ease, honours ReducedFx).
  - Rare hatches get `Hud.RareHatch` / `RareHatchController`, using `Hud.Templates.RareReveal`.
  - The Galaxy opening runs fly to centre → TAP TO CRACK ×3 → sunburst → cards → CLAIM!, with the HUD hidden and a watchdog.
- **Held pets already exist:** `PenService` gives held poses (hand, hug, overhead), sized at pen scale × 0.55 and capped at 10. Held bricks are weightless.
- **Celebrations:** `Client.Fx.Celebrate` (Purchase, Big, Surface, SurfaceUnlock, Deny). The DEV_NOTES standing rule says every new UI uses these effects.
- **Tutorial:** `Config.Tutorial` v3, 13 steps, Done = 13.
  - Steps: 1 Steal · 2 Escape · 3 Home · 4 Place · 5 Hatch · 6 Buy · 7 Wheel · 8 Index · 9 River · 10 River2 · 11 FreeSkip · 12 Trail.
  - There is a `TutorialVersion` migration (v1 → v2 → v3).
  - Completion grants 10 min of 2× Luck and the `TutorialComplete` card.
- **Funnel:** `OnboardingService` funnel v3, 16 steps:

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

  It was renumbered on 2026-10-14.
- **Codes:** `Codes.lua` holds LAUNCH, EGGROLL and THANKYOU (as of 2026-10-14).

---

## 1. The features

### Feature 1: Replace the eggs with "Vynx's_Opensource_Eggpack"

**What the user wants:** the egg models come from the pack now sitting in `Workspace` as `Vynx's_Opensource_Eggpack`.

1. **Audit the pack before using anything** (read-only):
   - List every model and part, its mesh, texture and decal ids, and its size and pivot.
   - **List every script inside it.** Don't run them.
     - Delete scripts the eggs don't need.
     - Read any that might matter, and report anything that `require`s an asset id, uses `loadstring`, fetches from HTTP, or touches Players or remotes. That pattern is a backdoor.
   - Run the open-use check from `handoffs/2026-10-15-retention-monetization-build.md` (section 3, Step A0) on its mesh and image ids. Anything that fails is listed and not used.
   - Look for a licence or credit note inside the pack. Report what it asks for (credit in the description, keep a credit script, etc.).
2. **Move it out of Workspace** after the audit: into `ReplicatedStorage.Assets.Eggs` (or where the existing egg assets live). It's an asset pack, not map, so moving it is fine, but say where it went.
3. **Map pack models to egg kinds** and show the table to the user (Q1):
   - zone eggs;
   - each variant;
   - HUGE;
   - starter and FreeSkip;
   - Galaxy (keep `Assets.GalaxyEgg` unless the user says otherwise).

   If the pack has fewer looks than there are kinds, propose recolours or texture swaps on one base model, in config.
4. **Swap the visuals, not the gameplay:**
   - Keep the existing collision and rolling body (size, physics, the carry and roll code). Attach the pack model as a **non-colliding, massless visual** welded to it, scaled from the current diameter.
   - Rolling must still look right: the visual spins with the roll and has no gap above the ground.
   - Keep every existing behaviour: glow for Shiny+, rarity colours, the clover and luck billboard, the weight label, BIG/HUGE/COLOSSAL pops, the incubating timer and hourglass, the night look, and the wheel scaling.
5. **Every place an egg is drawn** (list in section 0) uses one shared builder, e.g. `Shared.EggVisual.Build(kind, variant, diameter)`. No copies per controller. Leave the `EggStatue` as it is.
6. **Performance:** count parts per egg model. A server can have many eggs (5 nests per zone across six zones, plus carried and placed eggs). If a pack egg is heavy, simplify it or use one MeshPart. Report the counts.

### Feature 2: More visuals in egg-opening UIs

**What the user wants:** e.g. the player holding the new pet, or the pet in a viewport.

**Where it applies:**
- the normal hatch: `HatchCard` and the Epic+ full-screen reveal;
- the rare-hatch reveal;
- the Galaxy opening cards;
- the tutorial's first hatch (the Owl "Mama!" moment).

**Build:**
1. **Pet in a viewport on every hatch card and Galaxy card.**
   - A `ViewportFrame` with the pet model, slowly turning, lit, with a rarity-coloured backdrop or rim.
   - Use a `WorldModel` inside the ViewportFrame if the pet plays an idle animation.
   - One shared helper, e.g. `Client.UI.PetViewport.Mount(frame, petId)`. Clean up the clone when the card closes.
2. **"You got it!" hold moment** (Q2):
   - After the reveal, the player's character **holds the new pet up** for about 2 s, reusing `PenService`'s held-pet poses (overhead for Epic+, hug for lower).
   - The camera eases to the front of the character, then back.
   - Then the pet goes to the pen as it does today (auto-place stays).
   - Skip the camera move when the player is carrying an egg, on the wheel, or in a chase. Shorten it with ReducedFx.
   - Alternative if the camera move feels intrusive: show the **player's avatar holding the pet inside the card's viewport**:
     - clone the character with `Archivable = true` into a `WorldModel`;
     - play a hold animation on that clone;
     - attach the pet model above its hands.
3. **Scale with rarity:** Common gets the card with the viewport; Rare adds the hold; Epic+ adds the full-screen reveal plus the hold. Rarity colours come from config.
4. **Phones:** check every card at 667×375. The viewport must be readable at small sizes, and the card must not cover the Tutorial card during the tutorial.
5. **Use the `Celebrate` effects** for the beats (standing rule), and the art direction skill for every new UI.

### Feature 3: Fix the lobby's Biggest Egg pedestal

**Intended:** the player with today's biggest egg stands **on top of the pedestal** and holds **their egg above their head**.

**Bug:** their avatar appears **very far from the podium**. R15 has many quirks here.

1. **Find the code:**
   - `PlazaService` owns the Biggest Egg board. Find what builds the holder's figure, and whether it's the live player or a display rig.
   - Report how the figure is created (`Players:CreateHumanoidModelFromDescription` / `FromUserId`, a clone, or the player's own character) and how it is positioned.
2. **Likely causes to check, in order:**
   - **Pivot vs. root.**
     - `Model:PivotTo` moves the model's **WorldPivot**, which on a freshly built rig may not sit at the HumanoidRootPart.
     - `SetPrimaryPartCFrame` / `MoveTo` with no PrimaryPart, or `MoveTo`'s "find free space above" behaviour, can push it away.
     - **Fix:** set `PrimaryPart = HumanoidRootPart` and place by the root: `root.CFrame = pedestalTopCFrame * CFrame.new(0, humanoid.HipHeight + root.Size.Y/2, 0)`.
   - **Physics fling.**
     - An unanchored rig with a big welded egg falls, collides or gets flung.
     - **Fix:**
       - anchor **only** the HumanoidRootPart;
       - make the egg and accessories `Massless`, `CanCollide = false`, `CanQuery = false`;
       - set the humanoid to a static state (e.g. `PlatformStand`, or turn off `EvaluateStateMachine` on a display rig).
   - **Scale.** R15 body scales (`BodyHeightScale` etc.) change HipHeight and part sizes after the description applies. Compute the offset **after** `ApplyDescription` finishes.
   - **The egg itself.**
     - A very large egg welded at the hands drags the model, or its bounding box shifts the pivot.
     - **Fix:**
       - attach the egg with a `RigidConstraint` or `WeldConstraint` to an attachment above the head;
       - cap the **display** size so it reads well and doesn't dwarf the plaza (config: `BiggestEgg.DisplayMaxDiameter`);
       - centre the egg at head-top + egg radius + a small gap.
   - **Streaming.** The figure should be a `Model` with `ModelStreamingMode = Atomic` (or Persistent), so clients don't get a half-streamed rig.
   - **Live player vs. statue.** If the design moves the *live* player onto the pedestal, check `Humanoid` state, network ownership and the player's own movement fighting the placement.
     - Recommend a **display rig** of the holder (built from their HumanoidDescription) so it works even when the holder is offline. The live player stays free to move.
     - Ask in Q3 if unclear.
3. **The pose:** both arms up holding the egg overhead.
   - Use an animation if the game has one. Otherwise set the R15 shoulder `Motor6D.C0` / `Transform` for a static overhead pose.
   - Check it on both R15 and R6 descriptions, since the game may allow both.
4. **Updates:** when a new holder takes the record, swap the rig and egg cleanly: destroy the old one, no leaks, rate-limited.
   - Show the holder's name and egg weight on the existing board.
   - If no record exists today, show an empty pedestal or a "Your egg here!" sign.
5. **Use the new egg models** from feature 1 for the held egg.
6. **Screenshots** from the plaza camera and from a player standing in front of the pedestal, with a small and a huge record egg.

### Feature 4: Funnel hygiene

**Why:** the user's funnel (26 users, 23% reach Done) mixes three funnel versions. Roblox files funnel events by step number and shows the latest name, so older players are counted under today's names. The data also lags 1–2 days.

1. **Trace the 16 events on a wiped save** (needs the user's OK for a solo playtest). Each fires once, in order, for a new save; returning saves fire none.
2. **Versioning rule**, written into `DEV_NOTES.md` and a header comment in `OnboardingService`:
   - Never renumber or rename a live step.
   - When the step list changes (feature 5), ship it as a **new, dated version** in one publish.
   - Record the date and time so the user filters the Funnels page from then on.
3. **A light spawn-bounce signal:** on `PlayerRemoving` for new players who never reached `LeftSpawn`, log `SecondsInSession` and `TutorialStep` (custom analytics event). This tells bots and AFK accounts from confused players. No new remotes.

### Feature 5: A shorter tutorial (decision Q4)

**Why:** 12 steps after joining is long. Hud's Guide (in this repo, `huds-guide/08 Retention, Onboarding and Playtime/`) says:
- at 95% per step, 5 steps complete about 77% and 15 steps about 46%;
- keep the tutorial to the core loop and teach the rest in context.

The last step is the $5,000 Green Trail, a weaker buy than Wheel 2. The `NextGoal` chip already leads players to the trail afterwards.

**Proposal:** remove step 12 Trail, so the tutorial ends when the FreeSkip egg hatches, with the same completion reward and card. **Verify first** that the step still exists; the user may already have changed it.

**Must handle:**
- **A `TutorialVersion` v3 → v4 migration.** Saves on the removed step go to Done, with the completion reward granted exactly once. No save gets stuck.
- **A new dated funnel version** in the same publish (feature 4).
- **Every reader of the step list:**
  - `TutorialService` / `TutorialController`;
  - the HUD reveal gates (Done reveals the side bar);
  - `OnboardingService`;
  - hints and quests gated on `Tutorial.InProgress`;
  - the FreeSkip code, guard grace, the night exemption and the personal starter egg;
  - the other handoffs' features, if built.

### Feature 6: The ROLLGIANT code

The game page now says: "🎁 CODE: ROLLGIANT for a free boost! (Codes button on the left)".

1. **Find where codes are entered today** and tell the user exactly where, so the page text can be corrected. As of 2026-10-14 the left HUD bar held Shop, Daily, Quests and Rebirth only.
2. **Add ROLLGIANT (Q5):** recommend 15 min of 2× Speed. That's not a random item, so no odds rules apply. One claim per account, through the existing code path.
3. If there's no visible way to enter codes, propose one in the Step B message, e.g. a "Codes" entry in Settings, or a side-bar button revealed after the tutorial. Don't invent it unasked.

---

## 2. Method (director)

### Step A: fact base (read-only), in parallel

Launch **four audit subagents at once**:
1. feature 1 (the egg pack audit and every place an egg is drawn);
2. feature 2;
3. feature 3;
4. features 4–6.

They only read: `script_read` / `script_grep` / `search_game_tree` / `inspect_instance`, and `loadstring(Source)` for compile checks. The pack audit must **not run** the pack's scripts.

**Prompt template:**

```text
You are auditing ROLL A GIANT EGG in Roblox Studio for planned work. READ-ONLY: no edits, no playtests,
no require() of server modules, never run scripts from imported packs. Studio: list_roblox_studios →
place 122907972658396.
Work: <PASTE THE FEATURE SECTION(S)>.
Game facts (verify, don't trust): <PASTE SECTION 0>.
Return:
1. The current behaviour, traced through the code: exact paths, line numbers, quoted lines.
   (Feature 1: the pack's contents, ids, scripts, licence note, part counts, and every egg-drawing site.
    Feature 2: every hatch/opening UI and the held-pet code. Feature 3: how the pedestal figure is built
    and positioned, and why it ends up far away; reproduce the numbers with execute_luau reads.)
2. The systems the change touches or could break (tutorial, funnel, HUD hide path, other handoffs).
3. Streaming, performance and security risks.
4. Questions only the user can answer, each with your recommendation.
5. A build plan: ordered steps, files, server vs client, effort XS–L.
Say "not found" with where you looked rather than guessing.
```

**Then run one skeptic subagent over the four audits:** "Re-check every path, line number and claim from the code yourself. Mark each CONFIRMED or WRONG, with evidence." Write the fixed fact base to `handoffs/2026-10-15-eggs-pedestal-tutorial-audit.md`.

### Step B: one decision message to the user

| # | Question | Recommendation |
|---|---|---|
| Q1 | The egg mapping table: which pack model for each zone, variant, HUGE, starter (Galaxy unchanged?), plus the open-use and licence results | Show the table with screenshots; the user picks |
| Q2 | The hold moment: a camera move on the real character, or the avatar holding the pet inside the card's viewport? | Viewport version for Common/Rare, camera move for Epic+ only |
| Q3 | Pedestal: a display rig of the holder (works offline), or the live player moved onto it? | Display rig |
| Q4 | Remove the Trail step, ending the tutorial at the FreeSkip hatch? | Yes |
| Q5 | ROLLGIANT reward, and where codes should be entered if no UI exists | 15 min 2× Speed; a Codes entry in Settings |
| Q6 | Ship in two publishes a few days apart? (A: features 1, 2, 3, 6; B: features 4, 5 with the new funnel version) | Yes, so the tutorial change can be read on its own |

### Step C: build

**Order:** publish A (feature 3 → feature 1 → feature 2 → feature 6), then publish B (feature 4, then feature 5). Feature 3 comes first because it's a bug fix and uses only one egg. Feature 2 comes after feature 1 so the cards use the new eggs. Tell the user when each batch is ready; **the user publishes**.

**For each feature:**
1. Reuse what the audit found. One egg-visual builder, one pet-viewport helper, one HUD hide path, one tutorial state.
2. **Server-authoritative:** the server owns the record holder, codes, tutorial steps and hatch results. Visuals are client-side.
3. Put every tuning number in config (display sizes, viewport spin, hold duration, camera ease, pedestal offsets).
4. Run a compile check (`loadstring(Source)` via `execute_luau` in Edit mode). Take a `screen_capture` for anything visual, on a phone profile (667×375) too.
5. **Run critic subagents in parallel on each finished batch:**
   - **Visual critic:** the art direction skill, plus UI and Mobile UI checklists. Screenshots of every egg kind, every hatch rarity, the Galaxy cards and the pedestal.
   - **Gameplay critic:** rolling, carrying, placing, the wheel and hatching still work with the new egg visuals. No floating or sunk eggs. The pedestal swaps holders cleanly.
   - **Security and performance critic:** the pack's scripts are gone or vetted; part counts; viewport clean-up; no leaks on holder swaps; codes rate-limited.
   Fix what they confirm.
6. Add a `DEV_NOTES.md` entry, newest first, under `## Changelog`. Include the funnel version and its date (feature 4).

**Subagent reading** (give each the ones for its job):

| Note | URL | Who |
|---|---|---|
| UI checklist | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/27%20-%20Checklists/UI%20Checklist.md | features 2, 6 |
| Mobile UI checklist | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/27%20-%20Checklists/Mobile%20UI%20Checklist.md | features 2, 6 |
| UI engineering guide | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/07%20-%20UI%20UX/Roblox%20UI%20Engineering%20Guide.md | feature 2 |
| StreamingEnabled and large worlds | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/17%20-%20Performance/StreamingEnabled%20and%20Large%20Worlds.md | features 1, 3 |
| Player psychology checklist | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/27%20-%20Checklists/Roblox%20Player%20Psychology%20Checklist.md | features 2, 5 |
| Hud's Guide, retention and onboarding | https://github.com/Apollo9360/roll-egg-notes/tree/main/huds-guide/08%20Retention%2C%20Onboarding%20and%20Playtime | features 4, 5 |

## 3. Rules (from the user; mandatory)

- **NEVER download or install anything as a skill** (or plugin) unless the user explicitly said so beforehand. The egg pack is a **game asset the user placed in Workspace**. Using it is allowed, after the audit.
- **No playtests without the user's permission.** Ask in the Step B message for one solo playtest per publish batch, on a wiped save where the tutorial is involved. Restore the user's save afterwards.
- **Before anything visual,** load the user's installed skill `roll-egg-art-direction` (`~/.claude/skills/roll-egg-art-direction/SKILL.md`) and follow it. Every new UI uses the `Celebrate` effects (the standing rule in DEV_NOTES).
- **Never move or rebuild the map.** Inspect before editing; don't override the user's changes. Leave the `EggStatue` alone. The pedestal fix positions the *figure*, not the pedestal.
- **Reference folders** (`REFERENCE_*`, `Quarantine`, `Snapshots_*`, `W1_Backups`, the decomp): read them freely. Use their image, sound and mesh assets only after the open-use check (retention handoff, section 3, Step A0). Never copy their scripts, UI layouts or models.
- **No Creator Hub.** Never click through it or enter credentials. Publishing and product ids are the user's job.
- **Edit-mode `require` caches stale modules.** Verify with `loadstring(Source)` or a clone parented next to its siblings. Never `require` `Shared.Net` a second time on the server.
- **UI is real instances in StarterGui; controllers only bind.** The viewport contents (pet clones, the avatar clone) are the runtime exception.
- **Every paid prompt goes through the in-game confirm.**

## 4. Stop points and finish

1. **After Step A:** send the Step B message and wait. Build only answered items; skip anything unanswered and keep going.
2. **After publish batch A is built:** stop and tell the user it's ready to publish. Start batch B only when the user says so.
3. **When done:**
   - Commit on `claude/stoic-lamport-jqadwi`:
     - the audit file;
     - a report at `handoffs/2026-10-15-eggs-pedestal-tutorial-report.md`;
     - screenshots in `handoffs/2026-10-15-refs/built-eggs-pedestal/`.
   - Tell the user it's ready to push.
4. **The report covers:**
   - the decisions made;
   - the egg pack audit (contents, scripts removed, open-use results, licence or credit);
   - the egg mapping table;
   - what was built (paths);
   - the pedestal root cause and fix;
   - the new tutorial step list;
   - the funnel version and the date to filter from;
   - the migration rules;
   - where codes are entered (so the game page text can be fixed);
   - compile results;
   - critic findings and fixes;
   - a playtest checklist for the user, covering:
     - every egg kind in a nest, rolling, on the wheel, placed and hatching (no floating, no gaps);
     - a Common, a Rare and an Epic+ hatch (viewport, hold moment, camera);
     - a Galaxy opening at x1 and x3;
     - the pedestal with a small and a huge record egg, and a holder change while you watch;
     - a brand-new save from spawn to Done, on PC and in Device Simulator at 667×375;
     - a save on the old Trail step after the migration;
     - redeeming ROLLGIANT twice (the second time is refused).
