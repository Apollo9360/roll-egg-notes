# Request: everything about the first 10 minutes (Roll and Steal an Egg!)

**Date:** 2026-10-14
**From:** the cloud session that maintains the `roblox-dev/` vault. It cannot reach Studio.
**To:** a local Claude Code session connected to Roblox Studio through MCP.
**Repo and branch:** https://github.com/apollo9360/roll-egg-notes, branch `claude/stoic-lamport-jqadwi`.
- If your local clone lacks the branch: `git fetch origin claude/stoic-lamport-jqadwi`, then `git checkout claude/stoic-lamport-jqadwi`.
- Commit locally; the user pushes from GitHub Desktop.

**Files this request refers to** (read them only where a section asks):
- `DEV_NOTES.md` (repo root): https://github.com/apollo9360/roll-egg-notes/blob/claude/stoic-lamport-jqadwi/DEV_NOTES.md. The copy in your local scratch workspace may be newer; use the newer one.
- The first-minute handoff: https://github.com/apollo9360/roll-egg-notes/blob/claude/stoic-lamport-jqadwi/handoffs/2026-10-13-first-minute-polish.md

## Goal

The cloud session will redesign the **first 10 minutes for a brand-new player** to be as catchy as possible. It needs the real current state of the game first. This request asks you to **gather information only**. Don't change the place.

What you report becomes the base for a concrete FTUE (first-time user experience) plan:
- a second-by-second timeline of the first 10 minutes;
- every point where a new player could get bored, confused or stuck;
- every place where a reward, a "wow" moment or a goal could be added.

The more exact numbers, paths, texts and screenshots you include, the better the plan.

## Rules for this task

- **Read-only.** Don't edit scripts, instances, configs or data. Don't move or rebuild anything in the map.
- **Playtests:** the standing rule is that the user playtests. **Ask the user before starting any playtest.**
  - If they allow one: run a solo playtest on a fresh save (use the admin data wipe if that's the normal way to get a new-player state), capture screenshots, then stop the playtest.
  - If they don't: verify everything statically (read scripts and configs; `screen_capture` in Edit mode). Say which parts were static only.
- **Edit-mode `require` caches modules.** To read config values, read the module Source or use a fresh clone placed next to its siblings. Say which method you used.
- **Consult-only folders:** never copy from `ServerStorage.REFERENCE_PARKED`, `Quarantine`, `Snapshots_*`, `W1_Backups` or the Steal an Egg decomp. You may *describe* the decomp if the user wants a comparison, but label it as the reference game.
- **Creator Hub:** don't use it.
- When a value can't be found, write "not found" and say where you looked. **Don't guess.**

## How to deliver

1. Write the report to `handoffs/2026-10-14-first-10-minutes-report.md` in the repo above, using the section numbers below.
2. Put screenshots in `handoffs/ftue-screenshots/`, named after the list in section 12.
3. Commit and push to `claude/stoic-lamport-jqadwi`.
4. Tell the user it's done, so they can tell the cloud session to read it.

Use exact instance paths (e.g. `ServerScriptService.Server.Services.TutorialService`), exact config values and exact on-screen text in quotes. Where code matters, quote the relevant lines rather than summarising them.

---

## What we already believe

From `DEV_NOTES.md` and earlier handoffs, up to 2026-10-13. **Confirm or correct each item.**

1. A new player spawns at `LobbySpawn` around (188, 0.5, 256), with 10 Speed and $0.
2. The first Meadow nest is about 165 studs away at about (226, 0, 95), roughly 10 s of walking. The Meadow gold safe line is about halfway, near z≈176.
3. The golden `Lobby.EggStatue` (18×18×18 at about (188, 9, 224)) blocks the view from spawn toward the nests.
4. Lobby signs showed placeholder text:
   - "XX:XX to next HUGE EGG" on the entrance arch;
   - "Biggest egg today / -";
   - "treadmill" on the TrailStand signs.
5. The tutorial is driven by `Config.Tutorial`, TutorialService and TutorialController (card, beam, spotlight). Steps are roughly Steal → Escape → Home → Place → Hatch → BuyWheel → … → Index → Daily → Steal2 → Place2 → PickUp → WheelRoll → Hatch2 → Trail → Done (17).
6. The first delivered Meadow egg hatches a guaranteed **Epic Owl** with a 5 s hatch. Its income is about $24–69/s depending on egg size.
7. The next tutorial goal is to save $100 for the first hamster wheel.
8. The hamster wheel grows eggs without walking and speeds up hatching (3x at tier 1).
9. Eggs reset every 5:00 ("NEW EGGS IN 5:00"). There is a day/night cycle with a 10 s night.
10. **No analytics** existed on 2026-10-13. The first-minute handoff asked for an onboarding funnel.
11. The ad-driven player who quit did so 5–8 seconds after spawning, while walking toward the eggs.

---

## 1. Status of the first-minute polish handoff

For each item in `handoffs/2026-10-13-first-minute-polish.md`, say whether it was built, partly built or not built, with paths:
1. HUGE EGG timer;
2. Biggest-egg 3D display;
3. onboarding funnel analytics;
4. walk pickups and cues;
5. sign text fixes.

Also list every DEV_NOTES entry newer than "2026-10-13 — Skip timer prompts", one line each.

## 2. The first 10 seconds: spawn and first view

- **Spawn:** the exact spawn (path, position, facing direction). Is there one spawn or several? Does the player spawn on their own plot or in the shared lobby?
- **Camera:** the camera's starting position and direction at spawn. Is it the default camera, or is a CameraOffset, zoom limit or forced angle applied?
- **What's in view:** from spawn, at the default camera, what is visible within the first screen? List the big objects in view, left to right, and what is hidden behind the EggStatue.
- **Pointers to the first objective:** what points the player at it at second 0? Beam, arrows, chevrons, text, sound? When does each appear, and how long after spawn?
- **UI at second 0:** list every HUD and UI element visible at spawn on a phone-sized screen.
  - Give each element's path, text and approximate screen position.
  - Count them. Hud's Guide warns that about 10 elements on screen is already too many.
- **Popups:** do any popups, prompts or menus open automatically at join (daily reward, group, favourite, Robux offers, update log)? When?
- **Loading:** is there a loading screen? How long until the player can move?
- **Music and ambient sound** at spawn.

## 3. The tutorial, completely

Dump `Config.Tutorial` in full: every step in order. For each step give:
- id and number;
- title and card text, exactly as shown;
- target (what the beam or spotlight points at);
- completion condition, and how it's detected (event or polling);
- `Pre`, `SaveFor`, `Ack`, `AckSeconds` and other fields;
- reward on completion, if any;
- any sound or VFX on completion.

Then answer:
- **Visuals:** what does the card look like on phone and PC (size, position, font size)? Screenshot one step on each.
- **Hidden UI:** which HUD elements are hidden during the tutorial, and which stay?
- **Blocking:** are menus, purchases or other actions blocked during the tutorial?
- **Rejoining:** what happens on rejoin at every step? Can any step softlock: impossible to complete, target missing, or needing money the player can't get?
- **Idle help:** is there a forgiveness or idle timer that re-highlights the target if the player stands still?
- **Ending:** what happens when the tutorial ends? Is there a completion reward or celebration?
- **Skipping:** can a player skip the tutorial? Can a returning player be put back into it?

## 4. Numbers to simulate the first 10 minutes

The cloud session will build a minute-by-minute timeline from these, so exact values matter.

- **Movement:**
  - starting WalkSpeed and how Speed maps to WalkSpeed (formula);
  - how Speed is gained: per step, per second, from the wheel, from pickups or from Index rewards (formulas and config paths);
  - Speed at which Riverbank (300) and Canyon (8,000) open;
  - the time for a typical new player to reach 300 Speed.
- **Distances:** spawn → Meadow nest, nest → safe line, safe line → the player's plot, plot → shop, wheel board and trail stand. Measure in studs, and convert to seconds at the starting WalkSpeed.
- **Meadow guard:**
  - WalkSpeed, WakeSeconds and HitReach;
  - what wakes it;
  - whether it can catch a player who walks straight home with the first egg;
  - what happens on a catch (egg returned to the nest? penalty?).
- **Eggs:**
  - GrowthPerStud, MaxSize and the cap formula (`Economy.Egg.Cap`);
  - start size range;
  - the luck formula and the luck the first egg typically has when placed;
  - the size bonus for the first egg;
  - how the egg slows the player (the speed penalty while carrying, by size);
  - the egg's typical size after rolling it straight home.
- **Hatching:**
  - hatch times per pet;
  - the first egg's forced 5 s hatch;
  - the `TutorialEggMinHatchSeconds` (40) for the second egg;
  - wheel hatch speed-ups.
- **Income:**
  - the formula for a pet's $/s (BaseIncome × size × traits × rebirth × pass multipliers);
  - the expected $/s after the first Owl, and after the second egg.
- **Prices of everything a new player can buy in the first 10 minutes,** in order of cost:
  - hamster wheel tiers;
  - trails;
  - Speed Packs;
  - plot or pen upgrades;
  - shop items paid in game currency.
  For each, give its effect and how long the expected income takes to afford it.
- **Other systems:** rebirth requirements and rewards; Index rewards (`IndexSpeedReward` per pet); daily rewards; quests (the first three quests and their rewards).
- **Timers:** egg reset cadence and night cadence. What happens if a reset or night hits during the first steal, or while carrying?

## 5. The core loop, felt from the player's side

For each moment below, describe exactly what the player sees, hears and feels:
- **Grabbing the egg:** the prompt and its key; hold or press, and for how long; how the egg attaches; the first roll.
- **Guard wakes:** "!!", sound, camera, RUN banner, vignette.
- **The chase:** how close the guard gets for a typical new player; music change.
- **Crossing the safe line:** what happens on screen and in audio. Is there a "SAFE!" moment?
- **Rolling home and growth feedback:**
  - the size number, the clover luck tag and how it ticks up;
  - any milestone pops ("x2!").
- **Placing on the plot:** the prompt; the drop-in animation.
- **Hatch reveal:** the full sequence (anticipation, crack, reveal, rarity colours, sound, camera) and its length. Is the Epic Owl presented as a big deal?
- **First income:** how "+$N" is shown; whether the money counter animates; whether cash must be collected or arrives automatically.
- **First purchase (wheel):** the "drop from the sky" moment; feedback.

For each moment, list the sound and VFX assets used, and say if any moment has **no** sound or VFX.

## 6. Goals and reasons to stay

- **The visible next goal** at each minute, 0–10. Is there always one on screen, with a progress bar or a "$x / $y" ticker?
- **Big numbers and rarity moments** a new player can realistically hit in 10 minutes: Golden or Rainbow egg variants, size tiers (Big, Huge, Colossal), rare pets. What is the chance of one in the first 10 minutes?
- **Social proof:** visible server announcements, leaderboards, other players' pets on plots, and the Biggest Egg board.
- **Upcoming events visible to a new player:** HUGE EGG timer, night run, Admin Abuse or similar.
- **Leaving:** is there anything when a player leaves or opens the menu, such as offline earnings or "come back" rewards?

## 7. Monetisation in the first 10 minutes

- Every Robux prompt or offer a new player can see in the first 10 minutes: where it is, when it appears, and the price.
- Are any shown automatically?
- Game passes and developer products that exist: id, name, price, effect.
- Is any purchase blocked during the tutorial?
- Are the cash and Robux buttons next to each other, so a mis-tap could buy something?

## 8. Multiplayer effects on a new player

- Server size (MaxPlayers) and plot count. How plots are assigned. What happens if all plots are taken.
- Can other players affect a new player in the first 10 minutes? Stealing from their nest, blocking, hitting, taking their dropped egg, PvP tools?
- Is there any protection or grace period for new players?

## 9. Devices and input

- **Phone:** can every tutorial step be completed on a phone? Check prompt hold times, tap targets and the carry card size, with screenshots in Device Simulator on a small phone (e.g. iPhone SE size) and a tablet.
- **Gamepad:** can every step be completed with a gamepad? Does any step need a mouse click on a part (ClickDetector)?
- **Prompts:** ProximityPrompt `MaxActivationDistance`, `HoldDuration` and `RequiresLineOfSight` for the nest egg, the plot, the hatch and the wheel.
- **Text:** any text that is clipped or too small on phone.

## 10. Errors and performance

- Run `get_console_output` after loading (and after a playtest, if allowed). List every error and warning, with its script path.
- Approximate instance count in Workspace, and whether StreamingEnabled is on.
- Any obvious per-frame loops in client controllers that could hurt low-end phones.

## 11. Analytics

- Is AnalyticsService used anywhere now? Which events and funnel steps?
- If not, which hook points would each tutorial step use, e.g. `TutorialService.setStep`?

## 12. Screenshots

Use Edit-mode `screen_capture` unless a playtest is allowed. Name them exactly:
- `01-spawn-view-default-camera.png`: what a new player sees at spawn.
- `02-spawn-view-phone.png`: the same in Device Simulator, small phone.
- `03-lobby-top-down.png`: the lobby from above: spawn, EggStatue, plots, boards and the arch.
- `04-path-spawn-to-meadow-nest.png`: the walk to the first nest.
- `05-meadow-nest-and-guard.png`: the nest, nest eggs and the Mother Hen as built.
- `06-safe-zone-line.png`: the gold line up close.
- `07-tutorial-card-step1.png`: the tutorial card and beam at step 1.
- `08-carried-egg-billboard.png`: a rolled egg with its size and clover tag (playtest only).
- `09-plot-with-egg-incubating.png`
- `10-hatch-reveal.png`: the hatch reveal UI (playtest only).
- `11-hud-full-phone.png`: the HUD with menus closed, small phone.
- `12-shop-open.png` and `13-index-open.png`.
- `14-riverbank-guard.png` and `15-canyon-guard.png`.
- `16-pets-in-pen.png`: hatched pets as they look in the pen.

## 13. Your own read

After gathering everything, add a short section with:
- the **five biggest problems** you'd expect a new 9-year-old on a phone to hit in the first 10 minutes;
- the **five best moments** the game already has.

Label it as your opinion; the facts above come first.
