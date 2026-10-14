# Plan: make the first 10 minutes catchy (Roll and Steal an Egg!)

**Date:** 2026-10-14
**Written by:** the cloud session, from the local Studio session's report of 2026-10-14 (playtested on a wiped save).
**Built by:** a local Claude Code session connected to Roblox Studio (place 122907972658396).
**Repo:** https://github.com/apollo9360/roll-egg-notes, branch `claude/stoic-lamport-jqadwi`.
- If your clone lacks the branch: `git fetch origin claude/stoic-lamport-jqadwi`, then `git checkout claude/stoic-lamport-jqadwi`.
- Commit locally; the user pushes from GitHub Desktop.

Everything needed is in this file. The source report (`handoffs/2026-10-14-first-10-minutes-report.md`) is optional background.

---

## 1. Where the game is today

All numbers are for a normal new player without passes, computed from the configs.

| Time | What happens | Problem |
|---|---|---|
| 0:00 | Spawns on their own plot porch, facing the lobby. The golden EggStatue blocks the view of the nest. **15 HUD elements**, 8 of them buttons, including Shop (R$), Daily (red dot), Quests and Rebirth. | Too much UI before the first action |
| 0:10 | A "TIP: Traps stop guards too…" pill appears **on top of** "Meadow nest: N studs". | Covers the one useful line; PvP talk before they've played |
| 0:10–0:15 | Grab the egg (hold E 0.6 s). The Mother Hen sleeps 16 studs away and wakes 1.2 s after the grab. | A kid who grabs and pauses is **caught in ~1.5 s** and loses the egg; the tutorial resets to step 1 |
| 0:15–0:35 | Rolls home; the egg grows (~1.05 → 2.3). Step 2 says "Kite the Mother Hen for more", which **the code no longer rewards**. | Wrong text (26 words); no milestone pops while it grows |
| 0:35–0:50 | Place, 5 s hatch, Epic Owl (~$85/s). Hatch card shows for 2.9 s. | The Epic gets no special beat. The pet must be equipped from the hotbar and clicked into the pen ("Place your pet"): an extra chore |
| 0:50–1:30 | "Save $100…" for the wheel. The Owl earns that in ~1 s. | The "save" text is meaningless. Buying is via small world boards (fiddly on a phone; untested on gamepad) |
| 1:30–3:00 | Index and Daily steps **dim the whole screen** for up to 120 s each. Then "You're protected" (9 s). | Two chores and a PvP warning. The Index claim (**+320 Speed, which unlocks Riverbank**) isn't even required |
| 3:00–7:00 | Second egg: steal, place, pick back up, roll in the wheel, hatch (~4 min, 75 s timer). | Teaches a niche trick before the player cares; long dead time |
| 7:00–8:00 | "Get a trail" ($5,000, with a $ progress bar). | OK |
| ~8:00 | "Tutorial complete!" toast. No reward. Then nothing on screen says what to do. | Flat ending; no next goal |

**Structural risks that can wreck any first session:**
- **Night at every 5:00 reset** sends players in the corridor home and deletes their carried egg, tutorial players included.
- **Shared nests** hold 5 eggs and refill only at the reset, so a newcomer can find the Meadow nest empty for up to 5 minutes.
- **5 plots, but MaxPlayers reads 60.** A 6th player gets no plot and can never finish step 3.

**What already works well (keep it):**
- the steal: hold, "!", chase music, "RUN!!";
- the gold safe line with its curtain burst;
- the egg visibly growing;
- the egg's drop-in and the clover luck count-up;
- the wheel falling from the sky;
- Speed numbers flying into the counter;
- the walk pickups and chevrons.

## 2. The target: the first 10 minutes after this plan

Design rules:
- something gets better every 30–60 s;
- one goal on screen at all times;
- no long walk before the first reward;
- no chores before the fun;
- a "new zone" moment early.

| Time | Beat | Feeling |
|---|---|---|
| 0:00 | Clean HUD: money, Speed and the tutorial card only. Beam and chevrons toward the nest; pickups tick Speed up. | "I know where to go" |
| 0:10 | "Hold E to steal!" Grab, then the hen wakes a little slower for a first-timer and **just misses** them. | Thrill, no punishment |
| 0:15–0:35 | Rolling home: pops at size milestones ("BIG EGG!", the luck number jumping), then a gold "SAFE!" jingle at the line. | "My egg is getting better because I moved" |
| 0:40 | Hatch: a full **EPIC!** reveal; the Owl lands in the pen by itself. Money starts climbing. | First big win |
| 0:50 | The HUD reveals the Shop-free **"Buy" chip**: one tap buys the wheel, then the pen, then Wheel T2. Each pops in with a sound. | Number go up, three times in a minute |
| 1:30 | The Index button **pops in** glowing: "Claim your Owl's reward!" The claim pays +320 Speed, then **"RIVERBANK UNLOCKED!"** | A new world opens at minute 2 |
| 2:00–4:00 | "Steal from the River Croc!" New zone, new guard, bigger luck (LuckBase 25 vs 5), new pets. | Novelty and danger |
| 4:00–5:00 | Riverbank egg home. A hint shows the wheel trick ("Roll it in your wheel: hatches 3× faster!"). It hatches; the new pet goes in the Index, with **900+ Speed** to claim. | Progress jumps |
| 5:00–6:00 | "Buy the Green Trail" with a $ ticker. | A real goal, with saving |
| ~6:00 | **TUTORIAL COMPLETE!** Confetti, plus a free **2× Luck for 10 minutes**. Quests and Daily buttons pop in. | Reward, not just a toast |
| 6:00–10:00 | The **Next Goal chip** always shows the next buy or unlock: Wheel T3, Pen L3, then "Canyon: 8,000 Speed" with a progress bar. 2× Luck makes a Golden Goose or Heron likely. | "One more egg" |

## 3. Decisions for the user (ask before building these items)

| # | Decision | Recommendation |
|---|---|---|
| D1 | Server size vs plots | Set Game Settings → Places → **Max Players to 5** (matches the plots), unless the user wants to add plots |
| D2 | Riverbank inside the tutorial, replacing the second-Meadow-egg sequence (steps 11–15) | Yes |
| D3 | Remove the Daily and Protection steps from the tutorial. Daily opens on the **next** join instead; protection becomes a one-line toast after the tutorial | Yes |
| D4 | Tutorial completion reward | Free 2× Luck for 10 minutes. Uses the existing Luck boost the Day 7 reward grants |
| D5 | First-timer guard grace (slower wake, no hop, near miss) during the first Meadow and Riverbank steals | Yes |
| D6 | Night doesn't send home or delete the egg of a player still in the tutorial | Yes |
| D7 | EggStatue blocking the view from spawn | Leave it; the beacon pillar is visible above it. Moving the map needs the user's say-so |

Build anything not listed here without asking. Don't build a listed item until the user answers.

## 4. Work items

Effort: XS (< 30 min), S (< 2 h), M (half a day), L (a day or more). Impact is on bounce, playtime and return days.

### P0: blockers (do first)

**P0.1 Plot cap (D1).** Effort XS · Impact Very high (when servers fill).
- If the user picks Max Players = 5, they change it in Game Settings. No code.
- Also make `PlotService.assign` failure visible: a player without a plot should never be stranded. Today `warn("[PlotService] No free plot …")` is the only effect. Show a toast and teleport them to a new server with `TeleportService:TeleportAsync(game.PlaceId, {player})`, retried with backoff.

**P0.2 Night and tutorial players (D6).** Effort S · Impact High.
- In `ServerScriptService.Server.Services.EggService`, the night-start block (report: lines 1026–1042) loses carried eggs and sends corridor players home.
- Skip both for players whose `TutorialStep < Tutorial.Done`. Their carried egg stays, and they aren't teleported.
- Grabbing at night stays refused (EggService line 580), but a tutorial player who is carrying keeps their egg.

**P0.3 Always an egg for a newcomer.** Effort S · Impact High.
- When a player at tutorial step 1 (or the Riverbank steal step) comes within ~40 studs of a nest that has no eggs, spawn one egg in that nest for them.
- Use the existing nest-egg spawn path in EggService. Only the player at that step may grab it; others get the normal "taken" behaviour.
- Rate-limit it to one per player per reset.

**P0.4 Hints during the tutorial.** Effort XS · Impact Med.
- In `HintController`, don't show `Tutorial.Hints.Trap` (or any PvP hint) until `TutorialStep >= Done`.
- Also never place a hint pill over `StarterGui.Hud.FirstWalk`. `HudController` re-stacks Tutorial → Banners → HintPill → Toasts every frame; put the FirstWalk line in that stack.

### P1: the new first 10 minutes

**P1.1 Rewrite the tutorial steps (D2, D3).** Effort M · Impact Very high.

New `ReplicatedStorage.Shared.Config.Tutorial` steps. Keep the card text short. `{Action}` is "Click" or "Tap" as today. `{Key}` is a **new** token to add the same way: "E" on PC, the gamepad button (X) on gamepad, and "Tap and hold" wording on touch.

| # | Id | Title / body | Done when |
|---|---|---|---|
| 1 | Steal | "Steal an egg!" / "Hold {Key} on an egg at the Meadow nest." | `EggService.Grabbed` |
| 2 | Escape | "Run!" / "Cross the gold line. Your egg grows as it rolls!" | `EggService.ReachedSafety` |
| 3 | Home | "Safe!" / "Roll it onto your plot." | `EggService.EnteredPlot` |
| 4 | Place | "Drop it" / "{Action} your plot to set it down." | `EggService.Delivered` |
| 5 | Hatch | "Hatch it!" / "Hold {Key} on the egg when it says READY." | `PenService.Hatched` |
| 6 | Buy | "Buy a hamster wheel!" / "Tap Buy below." | `data.WheelTier >= 1` |
| 7 | Wheel | "Run in your wheel!" / "Speed takes you to new zones." | on wheel 3 s (as today) |
| 8 | Index | "Claim your reward!" / "Open the Index and claim your Owl." | Index reward for the Owl **claimed** (server), then closed |
| 9 | River | "Riverbank unlocked!" / "Steal an egg from the River Croc!" | `Grabbed` with biome Riverbank |
| 10 | River2 | "Roll it home!" / "Drop it on your plot." | `Delivered` of that egg |
| 11 | Trail | "Get a trail" / "Buy the Green Trail in the lobby." (keep `SaveFor = 5000`) | `Trails.Owned >= 1` |
| — | Done | reward (P1.7) | |

Notes:
- **Saved progress uses step numbers** (`Tutorial.Done = #Steps + 1`, legacy markers `LegacyDone2`, `LegacyDone3`, plus `inferStart` rules in `TutorialService`). Add a `TutorialVersion = 2` field to player data, and map old saves on load: old steps 1–7 keep their number; old 8–16 map to 8, or to Done if `Trails.Owned >= 1`. Keep the existing veteran skip (`WheelTier >= 1` → Done) only for players with no `TutorialVersion`.
- **Step 8 must require the claim.** Today the step advances on closing the Index even if nothing was claimed (observed). Advance only when IndexService records the Owl claim.
- **Riverbank unlock check:** the Owl's Index reward is +320 Speed, so 36 + 320 = 356 ≥ 300. Confirm `IndexService`'s cap ("below the second zone gate above current Speed") doesn't clip it for a player at 36 Speed. If it does, step 9 needs a direct unlock.
- **Remove** the old Daily, Protection, Steal2, Place2, PickUp, WheelRoll and Hatch2 steps from the mandatory flow. The wheel-hatch trick becomes the contextual hint in P1.8.
- **Re-check the "Place your pet" `Pre` step.** With P1.3 (auto-place) it disappears.
- Update `OnboardingService`'s funnel step list to match (P1.10).

**P1.2 First-timer guard grace (D5).** Effort S · Impact High.
- In `GuardService`, for the guard woken by a player whose `TutorialStep` is 1–2 (Meadow) or 9–10 (Riverbank) and `Stats.TimesCaught == 0`:
  - `WakeSeconds` + 1.3 s (Meadow 1.2 → 2.5);
  - no `Hop` burst;
  - if it gets within `HitReach + 2` studs, it skids to a stop for 0.8 s with a "!" and a dust puff. This is the near miss: it makes a chase moment, not a catch.
- After one tutorial escape per zone, the guard behaves normally.
- If a catch still happens during the tutorial, drop the egg next to the player rather than returning it to the nest.

**P1.3 Hatch payoff for the first pet.** Effort M · Impact High.
- **Auto-place the first pet in the pen** (`PenService`) and skip the equip-from-hotbar step.
- **Make the reveal bigger for Epic and above** (`HudController` hatch card, lines ~734–738, and `PlotEggController`):
  - card time 2.9 s → 4.5 s;
  - a full-screen rarity sunburst in the rarity colour;
  - a big "EPIC!" title that pops in;
  - `HatchRare` + confetti;
  - the camera eases toward the pet for 1 s, honouring Reduce Motion.
- Keep "Mama!" for the first hatch.

**P1.4 Buy chip in the HUD.** Effort M · Impact High.
- A new real instance `StarterGui.Hud.NextGoal`, under the tutorial card on phones and bottom-centre on PC.
- Contents: icon + name + "$x / $y" bar + a **Buy** button of at least 44 pt.
- It shows the cheapest useful next purchase from the plot boards: Wheel T1 → Pen L2 → Wheel T2 → Pen L3 → Green Trail → Wheel T3 …
- Buy fires the **same remote the boards use** (`RequestBuy`, e.g. `RequestBuy("Wheel", 1)`), so the server validation is unchanged.
- It pulses gold and plays a chime when affordable.
- This also fixes buying on phone and gamepad (SurfaceGui boards are fiddly or unverified).
- Hidden until step 6; always visible after the tutorial. After the trail, it points at the next zone: "Canyon: 8,000 Speed", with a Speed bar.

**P1.5 Progressive HUD reveal.** Effort S · Impact High.
- Show each HUD element only when the player needs it. Each reveal pops in (scale 0 → 1.15 → 1 over 0.2 s, plus `Unlock` sound) with a "NEW" badge for 5 s.

  | Element | Appears |
  |---|---|
  | `Hud.Stats` (Speed, money), tutorial card, FirstWalk line | from spawn |
  | `Hud.Tabs.Pets` | first hatch |
  | `Hud.Tabs.Eggs` | first placed egg |
  | `Hud.Tabs.Index` | step 8 |
  | `Hud.Left.Shop`, `Daily`, `Quests`, `Rebirth`, `Hud.Settings`, `Hud.Shield` | tutorial done |

- Returning players (`TutorialStep >= Done`) see everything at once.
- Settings stays reachable via the Roblox menu. If the user wants it from spawn, keep it.

**P1.6 Growth and safety beats.** Effort S · Impact Med–High.
- In `CarryController`, while carrying:
  - a pop banner and sound when the egg crosses the BIG (ratio 0.6) and HUGE (0.85) tiers ("BIG EGG!", "HUGE EGG!");
  - a quick pulse of the clover number each time luck rises by another ×2.
- At the safe line (`CarrySafe`), add a big gold **"SAFE!"** text burst on top of the existing curtain and `LockIn`.

**P1.7 Tutorial completion reward (D4).** Effort S · Impact High.
- On step Done (`TutorialService.setStep`), replace the toast with:
  - a full-screen "TUTORIAL COMPLETE!" card with confetti and `Unlock`;
  - a server-granted **2× Luck for 10 minutes**, using the same boost path as the Day 7 reward in `RetentionService`;
  - the P1.5 reveals of Shop, Daily and Quests, one after another.
- The boost timer shows on the HUD.

**P1.8 Contextual hints instead of tutorial steps.** Effort S · Impact Med.
- **Wheel trick:** when a player owns a wheel and has an incubating egg with more than 20 s left, show once: "Tip: roll it in your wheel to hatch 3× faster!". Uses the existing pick-up (F) and wheel hatch rate.
- **Daily:** show the Daily panel automatically on the **next** join (if a reward is claimable), not during the first session's tutorial.
- **Protection:** one toast after the tutorial: "You're protected for N more minutes."

**P1.9 Riverbank arrival.** Effort S · Impact Med.
- The first time a player's Speed passes 300, show "RIVERBANK UNLOCKED!": a banner, `Unlock`, and the TutorialArrow beam to the Riverbank nest.
- Do the same for Canyon at 8,000.
- These moments exist only as entrance-sign text today.

**P1.10 Funnel analytics.** Effort XS · Impact High (it's how every change gets measured).
- In `OnboardingService`, rename the funnel steps to the new tutorial: Joined, LeftSpawn, EnteredMeadow, ReachedNest, then one per new step, ending with Done.
- **Fix `LeftSpawn`:** it measures from `LobbySpawn`, but players spawn on their plot 15 studs away. Measure 25 studs from the player's own plot `Spawn` part.
- Roblox funnels are per-experience. To start a clean funnel after this change, log under a new funnel version if the API in use allows one; otherwise note the change date in `DEV_NOTES.md` so before and after can be compared.

### P2: polish (after P0 and P1 ship)

- **P2.1 Phone text.** `Hud.Tutorial` `CompactSize` is {0.4,0},{0.1,0}, which is ~38 px tall on a 375 px phone with `TextScaled` text. Raise the compact height so the body text is at least 14 px. Test in Device Simulator (the user does this; MCP can't). Effort XS.
- **P2.2 Walk pickups on rejoin** can be collected again each session (`FirstWalkService` line 75 tracks per session). Save it per account. Effort XS.
- **P2.3 Favourite prompt** once, after the first Legendary hatch or at 9 minutes, whichever comes first. Use `AvatarEditorService:PromptSetFavorite(game.PlaceId, Enum.AvatarItemType.Asset, true)`; check the signature in the current API reference before using it. Effort XS.
- **P2.4 Economy and progression analytics:** `AnalyticsService:LogEconomyEvent` for cash sources and sinks (wheel, pen, trail purchases; pet income in batches) and `LogProgressionEvent` for zone unlocks. Effort S.
- **P2.5 Consult-only sound errors:** the 33 "not authorized" sounds are all inside `ServerStorage.REFERENCE_PLEASEUSE`. They don't affect players, but they flood Output. Ask the user whether to remove that folder before publishing (it's on the pre-publish list in DEV_NOTES). Effort XS.

## 5. How to build it

1. **Before anything visual,** load the user's art-direction skill `roll-egg-art-direction` (`~/.claude/skills/roll-egg-art-direction/SKILL.md` on the user's machine) and follow it: Rubik / Fredoka styling, colours, strokes.
2. **Ask the user D1–D7** (section 3) in one message, and build the answered items only.
3. **Build in this order:** P0.1–P0.4 → P1.10 → P1.1 → P1.2 → P1.3 → P1.4 → P1.5 → P1.6 → P1.7 → P1.8 → P1.9 → P2.
4. **Verify each item** with a compile check (`loadstring(Source)` via `execute_luau` in Edit mode) and a `screen_capture`. Avoid `require` of server modules from the command bar: in the last session it broke data replication.
5. **Playtest only with the user's permission.** Use a fresh save (admin wipe) and the test account. Remember it owns 2× Money and 2× Growth, so observed income and growth are double.
6. **Log every change in `DEV_NOTES.md`,** newest first, under `## Changelog`. The last five features (HUGE EGG, Biggest Egg, FirstWalk, Onboarding funnel, sign fixes) were built without entries; add one catch-up entry for them too.

## 6. Rules

- **UI and world objects are real instances** in StarterGui and Workspace; controllers only bind. New UI (`Hud.NextGoal`, banners) is built as instances.
- **Never move or rebuild the map.** Inspect before editing; don't override the user's changes.
- **Server decides everything that matters:** purchases, rewards, boosts, tutorial progress, guard behaviour. The client only shows it.
- **Never copy** from `ServerStorage.REFERENCE_PARKED`, `REFERENCE_PLEASEUSE`, `Quarantine`, `Snapshots_*`, `W1_Backups` or the Steal an Egg decomp.
- **No Creator Hub.** If a change needs a new product or badge, list it for the user.

## 7. How we'll know it worked

After the change ships and ads run for a few days, compare the onboarding funnel in the Creator Dashboard. These are community benchmarks for simulators:

| Measure | Target |
|---|---|
| Tutorial completion | 75–85% (under 50% means something is broken) |
| Players still in game at 5 minutes | 60–65% |
| Average session | 15–20+ minutes |
| D1 retention | 10–15% |

Fix the biggest drop first, one change at a time, so it's clear what moved the number.

## 8. Finish

1. Commit on `claude/stoic-lamport-jqadwi` and tell the user it's ready to push.
2. Report:
   - what was built (paths);
   - which decisions the user made;
   - compile results;
   - before and after screenshots of spawn, the steal, the hatch and the Next Goal chip;
   - the new funnel step list;
   - a playtest checklist for the user, including a phone test in Device Simulator.
