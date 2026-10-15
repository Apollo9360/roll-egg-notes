# Report: the first 10 minutes (Roll and Steal an Egg!)

**Answers:** [2026-10-14-first-10-minutes-discovery.md](2026-10-14-first-10-minutes-discovery.md)
**Gathered by:** the local Claude Code session connected to Studio (studio_id `76744230-81ee-470c-8d7a-fc7a89b6c2a2`, place 122907972658396), 2026-10-13/14.
**Place changes:** none. No script, instance, config or data was edited. The map was not moved.

## How this was gathered (read first)

- **Config values** were read as module **Source text** via `execute_luau` in Edit mode (`.Source`, not `require`). No values below come from a cached `require`.
- **Playtests:** the user allowed them and wiped the save first. Two solo playtests ran on the admin test account:
  - **Run 1:** fresh save. It was abandoned after step 3 because a tester action broke data replication (see the "Tester artifact" note below).
  - **Run 2:** a rejoin of that same save, played from step 1 to step 16.
  - Both playtests were stopped afterwards.
- **The test account is NOT a normal new player.** It owns both game passes and is an admin. The player attributes read in play were:
  - `OwnsPass_Pass2xMoney=true`
  - `OwnsPass_Pass2xGrowth=true`
  - `PaidMoney=2`
  - `PaidGrowth=2`
  - `IsAdmin=true`

  So **observed income and egg growth are 2x** a real new player's. All "expected" numbers in section 4 are **computed pass-free** from the config formulas. Observed numbers are labelled "observed (2x passes)".
- **Tester artifact:** in run 1, I ran `require(ServerScriptService.Server.Services.DataService)` from the command bar. That created a second module instance.
  - Afterwards, `DataChanged` events stopped reaching the client and `GetSnapshot` timed out. The tutorial card froze on step 1 while the server was on step 4.
  - **This was caused by my command, not by the game.** It did not recur in run 2. Don't count it as a game bug.
- **Not possible through MCP:** Device Simulator (phone/tablet emulation), a gamepad, and touch input.
  - Screenshots `02` and `11` (phone) were **not captured**.
  - Phone notes in sections 2 and 9 are computed from the UI's Scale sizes. They are labelled as computed.
- **Viewport for all play screenshots:** 1297×789 (Studio window, PC).
- Line numbers refer to the current Source.

---

## What we already believe: confirmed or corrected

| # | Belief | Verdict |
|---|---|---|
| 1 | Spawn at LobbySpawn ~(188, 0.5, 256), 10 Speed, $0 | **Corrected.** LobbySpawn exists at (188.25, 0.5, 256.25), but `PlotService.spawnAtPlot` re-pivots every character onto **their own plot's `Spawn` part**. Plot1's is at (188.25, 0.15, 271.25), facing out of the plot (−Z, toward the arch). Observed spawn root: (188.25, 3.55, 271.25), look (0, 0, −1). Speed 10 and $0 are **confirmed** (`Economy.StartingSpeed = 10`, `StartingMoney = 0`). LobbySpawn is only used by a player who gets no plot (6th+ player, see section 8). |
| 2 | Nest ~165 studs at ~(226, 0, 95), ~10 s; safe line z≈176 | **Corrected distance.** The nest centre (`Biomes.Meadow.NestArea`) is at (226.25, 0.1, 96.25). From the plot spawn it is **179 studs** in a straight line; the HUD shows "Meadow nest: 180 studs". That is 11.2 s at WalkSpeed 16. Observed: **8.2 s** on rejoin, because the pickups raise WalkSpeed. The safe line is **confirmed** at z = 176.25 (Meadow GatePosts and the "SAFE ZONE" ground text). It is 95 studs from the plot spawn and 80 studs from the nest. |
| 3 | EggStatue blocks the view | **Confirmed, still there.** `Lobby.EggStatue` has a bounding box of 18×18×18 at (188.25, 9, 224.25), unchanged. It sits 47 studs straight ahead of the Plot1 spawn. The HUGE EGG billboard was raised above the arch to be readable over it (HugeEggController header). |
| 4 | Placeholder signs | **Corrected: fixed.** The arch shows "HUGE EGG SOON" in Edit mode and "HUGE EGG IN m:ss" in play. The Biggest Egg board shows "BIGGEST EGG TODAY / No egg yet today" with a 3D placeholder rig. The TrailStand reads "multiply wheel Speed gain" and "×N wheel Speed gain". No "treadmill" text is left on the stand. |
| 5 | Step list, Done = 17 | **Confirmed.** 16 steps; `Tutorial.Done = #Steps + 1 = 17`. Full dump in section 3. |
| 6 | Epic Owl, 5 s hatch, $24–69/s | **Partly corrected.** Owl and 5 s are confirmed (`FirstEggGuaranteedPet = "Owl"`, `FirstEggHatchSeconds = 5`). Income is **higher** than believed: the egg keeps growing on the walk home, then gets +0.5 size. Computed pass-free: **$53–133/s, typically ~$85/s**. Observed (2x passes): $172.2/s per Owl, $344.4/s shown. |
| 7 | Save $100 for the wheel | **Confirmed** (step 6 "Get faster"). It is reached in 1–2 s of Owl income, so the "save up" beat effectively doesn't exist. |
| 8 | Wheel grows eggs and speeds hatching (3x at tier 1) | **Confirmed.** Hatch rate = 1 + `WheelHatchBonus` 2 + 0.25·(tier−1) = 3x at tier 1. Growth while in the wheel = WalkSpeed × 0.15 × (1 + 0.25·(tier−1)) studs/s. |
| 9 | 5:00 reset, 10 s night | **Confirmed.** `Economy.Cycle`: DaySeconds 290 + NightSeconds 10. The HUD shows "NEW EGGS IN 5:00" and then "NIGHT · NEW EGGS IN 0:06". |
| 10 | No analytics | **Corrected.** `OnboardingService` logs an onboarding funnel through `AnalyticsService:LogOnboardingFunnelStepEvent`. It fired 12 times in the playtests (section 11). |
| 11 | The quitter left 5–8 s in | Not verifiable from the place: there is no session data in Studio. Note: at ~10 s a **"TIP: Traps stop guards too…"** pill covers the nest-distance line (section 2). |

---

## 1. Status of the first-minute polish handoff

| Item | Status | Paths |
|---|---|---|
| 1. HUGE EGG timer | **Built** | `ServerScriptService.Server.Services.HugeEggService` (248 lines). `StarterPlayer.StarterPlayerScripts.Client.Controllers.HugeEggController` (75 lines). Arch label `Workspace.World.Lobby.EntranceArch.Sign.SurfaceGui.Label` and raised billboard `EntranceArch.HugeEggMarker.HugeEggBillboard.Label`. FX template `ServerStorage.HugeEggFx`. Cadence: `EVERY_RESETS = 3`, so one HUGE egg per 3 egg resets (~15 min), in a weighted zone (Meadow most likely). Workspace attrs `HugeEggAt`, `HugeEggResetsLeft`, `HugeEggLive`. `EggService.SpawnHuge` starts it at zone MaxSize × `HUGE_START_MULT` 1.0, so it always hatches Huge or Colossal. |
| 2. Biggest-egg 3D display | **Built** | `ServerScriptService.Server.Services.BiggestEggService` (158 lines): OrderedDataStore `LB_BiggestEggDay_<yyyymmdd>`. `PlazaService` draws the board and the 3D holder. Instances: `Workspace.World.Lobby.BiggestEgg` (`BiggestEggBoard`, `HolderSpot.HolderTag`, `HolderPedestal`, `PlaceholderRig`). Edit text: "BIGGEST EGG TODAY / No egg yet today", tag "Nobody yet / Roll one and claim the spot!". |
| 3. Onboarding funnel analytics | **Built** | `ServerScriptService.Server.Services.OnboardingService` (119 lines). See section 11. |
| 4. Walk pickups and cues | **Built** | `ServerScriptService.Server.Services.FirstWalkService` (131 lines). `StarterPlayer…Controllers.FirstWalkController` (452 lines). Instances: `Workspace.World.Lobby.FirstWalk` (13 `Pickups`, 15 `Chevrons`, `NestMarker` at (226.25, 14, 96.25)) and `StarterGui.Hud.FirstWalk` (banner + "Meadow nest: N studs" line). |
| 5. Sign text fixes | **Built** | TrailStand header "TRAILS / multiply wheel Speed gain". Pedestals "×1.5 wheel Speed gain" … "×8 wheel Speed gain". |

**DEV_NOTES entries newer than "2026-10-13 — Skip timer prompts": none.** The newest entry in the repo's `DEV_NOTES.md` is still "Skip timer prompts". All five items above were built **without a DEV_NOTES entry**.

---

## 2. The first 10 seconds: spawn and first view

**Spawn**
- One SpawnLocation exists: `Workspace.World.Lobby.LobbySpawn` at (188.25, 0.5, 256.25), Orientation (0, 90, 0), Neutral, Enabled.
- **The player does not stay there.** `PlotService.spawnAtPlot` (lines 184–210) waits one frame, calls `RequestStreamAroundAsync`, then pivots the character:
  ```lua
  -- face out of the plot (back-row plots face the arch, side plots face the plaza)
  character:PivotTo(CFrame.lookAt(plot.Spawn, plot.Spawn + plot.SpawnFacing))
  ```
- The player spawns **on their own plot's porch**. Plot1 is at (188.25, 3.55, 271.25) facing −Z.
- Plots are assigned in index order 1..5, so a solo player always gets Plot1.

**Camera**
- Default `Custom` camera. `StarterPlayer.CameraMode = Classic`, `CameraMaxZoomDistance = 128`, `CameraMinZoomDistance = 0.5`, FOV 70.
- Observed at t ≈ 11 s: camera ~13 studs from the root. No forced angle and no CameraOffset at spawn.
- `FirstWalkController` adds a one-time FOV kick (+12) and streaks with the "corridor whoosh" on first entering the corridor. `FeelController` and `GuardFxController` only act on catch and chase.

**What's in view** (screenshot `01`; Edit overview in `03`, `04`)
- Left to right from the Plot1 spawn:
  - the Fuse Machine (purple, "FUSE MACHINE" sign) on the far left of the lobby;
  - the BIGGEST EGG TODAY board and 3D holder;
  - the golden EggStatue, dead centre;
  - the entrance arch with the "HUGE EGG IN m:ss" billboard above it;
  - the TRAILS stand;
  - the leaderboards (Biggest Egg, Top Earners, Most Rebirths) on the right;
  - Plot5 (side plot) on the far right.
- **Behind the EggStatue:** the arch opening and the Meadow beyond it, including the nest, the Mother Hen and the NestBeacon light pillar. The pillar is 91 studs tall and stays visible above the statue.
- In run 1 the player's own avatar (large wings accessory) also hid much of the centre of the screen. That is avatar-specific.

**Pointers to the first objective at second 0**

| Pointer | Path | When |
|---|---|---|
| Tutorial card "Steal an egg!" (step 1/16) | `StarterGui.Hud.Tutorial` | As soon as data arrives (~1–2 s). TutorialController shows the Steal card even at step 0. |
| Distance line "Meadow nest: 180 studs" | `StarterGui.Hud.FirstWalk.Distance`, driven by TutorialController | Same time. Updates live (observed 180 → 105 → 85 → 20). |
| Guide beam: scrolling chevron beam from the feet to the target | `ReplicatedStorage.TutorialArrow` | Same time. Shows as white chevrons at screen bottom-right in `01`. |
| 13 Speed pickups (+2 Speed each), bobbing and spinning | `Lobby.FirstWalk.Pickups` | At spawn, step 1 only. The first one is at (192.6, 3.5, 246.1), 25 studs ahead. |
| 15 ground chevrons lighting up in a wave toward the nest | `Lobby.FirstWalk.Chevrons` | At spawn, step 1 only. |
| NestMarker billboard (badge + "!" bar) and a Highlight on `Guard_Meadow` | `Lobby.FirstWalk.NestMarker` | Step 1 only. |
| NestBeacon light pillar | `Biomes.Meadow.NestBeacon` | Always. |
| Corridor whoosh: sound ("Swing"), streaks, FOV kick, banner | `FirstWalkController` | Once, on entering the corridor. |

**UI at second 0** (PC, 1297×789; read from `PlayerGui` at ~t = 11 s)

| # | Element (path) | Text | Screen position (x, y; size) |
|---|---|---|---|
| 1 | `Hud.Shield` | "PROTECTED 5:47" (counts down from 10:00) | top centre (528, ~8) |
| 2 | `Hud.Tutorial` | "Steal an egg!" / "Sneak up to the Meadow nest and hold to steal an egg." / "1/16" | top centre (372, 5; 552×78) |
| 3 | `Hud.FirstWalk` | "Meadow nest: 180 studs" | under the card (~545, 150) |
| 4 | Hint pill (runtime, HintController) | "TIP Traps stop guards too - drop one behind you in a chase." | **directly over item 3**, at ~10 s |
| 5–8 | `Hud.Left` | Shop (R$ badge), Daily (red dot), Quests, Rebirth | left (12, 185; 120×237) |
| 9 | `Hud.Settings` | "Settings" | left (12, 497) |
| 10–12 | `Hud.Tabs` | Eggs, Pets, Index | right (1216, 210; 68×347) |
| 13 | `Hud.Stats` | shoe "10", cash "$0", "+$0/s" | bottom-left (12, 600; 259×118) |
| 14 | `Hud.Timer` | "NEW EGGS IN / 4:46" | bottom-right (1102, 668; 180×48) |
| 15 | Over-head billboard | "PROTECTED" | over the avatar |
| 16 | Roblox hotbar (CoreGui Backpack, enabled) | "Bat", "Trap ×1" tools | bottom centre (not in screenshots: CoreGui isn't captured) |
| — | `Admin.Toggle` "ADMIN" | admin only | bottom-right |

- **Count: 15 game elements** (16 with the hotbar), of which **8 are buttons**. That is above the ~10 Hud's Guide warns about.
- Phone: `Hud.Tutorial` has `CompactSize {0.4,0},{0.1,0}` for touch and short screens. **Computed:** on a 667×375 landscape phone the card is ~267×38 px, with `TextScaled` Fredoka One for title, body and step. The body would render at roughly 8–10 px. Not verified in the simulator.

**Popups at join**
- None open automatically: no daily, group, favourite, Robux offer or update log. grep finds no `PromptSetFavorite` or group prompts.
- The Daily button gets a red dot. The tutorial opens nothing by itself.
- The **Trap hint** (`Tutorial.Hints.Trap`) fires at ~10 s. It triggers because the player has a PvP tool and `Tools.Trap > 0`, and every new player is given "Trap ×1".

**Loading**
- `ReplicatedFirst` is empty: no custom loading screen, only Roblox's default.
- The player can move as soon as the character spawns.
- The plot pivot happens one frame after spawn, after `RequestStreamAroundAsync`.
- In Studio the first screenshot (t ≈ 11 s) showed a fully playable state.

**Music and ambient sound**
- `SoundService.MusicDay` (rbxassetid://1836853637) loops from join.
- Chase music `MusicChase` plays during a chase (GuardFxController).
- All 28 named sounds in `ReplicatedStorage.Shared.Assets.Sounds` loaded (`IsLoaded = true`): Alarm, BossRoar, Click, Close, Coin, EggReady, Error, Grab, Grow, GuardPound, GuardWake, Hatch, HatchRare, Hit, Hover, LockIn, MusicChase, MusicDay, Notify, Open, Purchase, SpeedGain, StealAlarm, Swing, Trap, Unlock, WheelLand, WheelUpgrade.

---

## 3. The tutorial, completely

Source: `ReplicatedStorage.Shared.Config.Tutorial` (95 lines). Server: `ServerScriptService.Server.Services.TutorialService` (232 lines). Client: `…Controllers.TutorialController` (781 lines). `{Action}` becomes "Click" (PC) or "Tap" (touch).

| # | Id | Title / card text (exact) | Target / Highlight | Completion (how detected) | Other fields |
|---|---|---|---|---|---|
| 1 | Steal | "Steal an egg!" / "Sneak up to the Meadow nest and hold to steal an egg." | beam → `NestEgg` | `EggService.Grabbed` (event) → step 2 (or 3 if already safe) | — |
| 2 | Escape | "Roll it home!" / "Your egg grows while you roll it in its zone. Kite the Mother Hen for more, then cross the gold line into the bases: guards stop there." | `LockLine` | `EggService.ReachedSafety` (event, Heartbeat line check) → 3 | **Text vs code mismatch:** EggService grows the egg "anywhere" (header line 5) and has no kiting bonus (no "kite"/ChasedBy growth code found). |
| 3 | Home | "Safe!" / "Roll your egg onto your plot. It keeps growing with every step you take!" | `Plot` | `EggService.EnteredPlot` (event) → 4 | — |
| 4 | Place | "Place your egg" / "{Action} anywhere on your plot to set the egg down." | none | `EggService.Delivered` (event) → 5 | — |
| 5 | Hatch | "Hatch it!" / "When the timer says READY, hold on the egg to hatch it." | `PlacedEgg` | `PenService.Hatched` (event) → 6; 1 Hz fallback if no eggs and ≥1 pet | First egg: 5 s timer |
| 6 | BuyWheel | "Get faster" / "Your pet earns cash. Save $100 and buy your first hamster wheel on the Wheel board outside your plot." | `WheelBoard` | 1 Hz poll `data.WheelTier >= 1` → 7 | `Pre` until `PetPlaced`: "Place your pet" / "Pets only earn cash in your pen: equip your new pet from the hotbar, then {Action} inside your pen." target `Pen` |
| 7 | Wheel | "Run in your hamster wheel!" / "Run inside the hamster wheel outside your plot to gain Speed. More Speed = deeper zones = bigger eggs." | `Wheel` | 1 Hz poll: `OnWheel` for `WheelSecondsToFinish` = 3 s → 8 | — |
| 8 | Index | "Collect them all" / "Open the Index: every new pet you discover pays Speed and cash. Claim your reward, then close it!" | Highlight `Index` (spotlight) | client ack `CloseModal:Index`, or server auto after 120 + 2 s | AckSeconds 120. In-menu tip "Claim your reward!" |
| 9 | Daily | "Daily rewards" / "Come back every day: open Daily and claim today's free reward." | Highlight `Daily` (spotlight) | ack `CloseModal:Daily`, or auto after 122 s | AckSeconds 120 |
| 10 | Protection | "You're protected (for now)" / "New players can't be batted or trapped. When your shield ends, watch out for bats and traps in the wild zones!" | none | `Timer`: auto after 9 s (+2 s server) | AckSeconds 9 |
| 11 | Steal2 | "Steal a second egg" / "Grab another egg from a nest and roll it home. Your hamster wheel will help it hatch!" | `NestEgg` | 1 Hz state poll (`secondEgg`): carrying → 12 | — |
| 12 | Place2 | "Place it" / "Roll it onto your plot and {Action} a spot to set it down." | `Plot` | poll: not carrying and egg incubating → 13 | egg hatch ≥ `TutorialEggMinHatchSeconds` 40 s |
| 13 | PickUp | "Pick it back up" / "Placed eggs can roll again! Hold F on your new egg (or tap Roll) to pick it up." | `PlacedEgg` | poll: `CarriedHatchLeft` non-nil → 14 | — |
| 14 | WheelRoll | "Roll it in your wheel" / "Run in your hamster wheel with the egg: its hatch timer drops 3x faster!" | `Wheel` | poll: `hatchLeft <= 0` → 15 | — |
| 15 | Hatch2 | "Hatch it!" / "Timer done! {Action} your plot to set the egg down, then hold E on it to hatch." | `HatchEgg` | `PenService.Hatched`, or poll: no eggs → 16 | — |
| 16 | Trail | "Get a trail" / "Buy the Green Trail at the Trail Stand in the lobby. Trails multiply the Speed your hamster wheel gives!" | `TrailStand` | poll: `data.Trails.Owned >= 1` → 17 | `SaveFor` = 5,000 (card shows $ progress) |

**Rewards and FX per step**
- No step grants a reward by itself.
- Step changes play `Notify` (TutorialController lines 461 and 683).
- The **first hatch** gets a "Mama!" speech bubble over the pet, confetti, and `HatchRare` (TutorialController line 499).
- Step 17 (Done) shows a toast: "Tutorial complete! Get faster to reach deeper zones." (`TutorialService.setStep`, line 58). There is no reward and no other celebration.

**Observed step timing (run 2, rejoin at step 1, Speed 22, 2x passes)**
- From plot spawn: step 2 at 8.8 s (grab), 3 at 12.9 s (safe line), 4 at 19.2 s (on the plot).
- Place → step 5. Hatch at +5 s. Then step 6 ("Place your pet" Pre).
- Pet placed → step 6 shows $1.7K already, so the $100 wheel is instant.
- Wheel bought → step 7. 3 s in the wheel → step 8.
- Index opened and closed (**the reward was not claimed and the step still advanced**) → 9.
- Daily claimed and closed → 10. After 11 s → 11.
- The second egg took ~4 min, including the walk; its hatch timer was 75 s (`ReadyAt − DropAt`). Then step 16.

**Visuals**
- `StarterGui.Hud.Tutorial`: Size `{0.5,0},{0.1,0}`, Position `{0.5,0},{0,64}`, top centre.
- Contents: star icon, Title, Body and "N/16" step counter, all `TextScaled` Fredoka One. `CompactSize {0.4,0},{0.1,0}` on touch.
- On PC (1297×789) it is 552×78 px (`01`, `09`, `16`).
- Phone: **not captured** (no Device Simulator via MCP); computed size is in section 2.

**Hidden UI during the tutorial**
- No HUD buttons are hidden at any step (observed at steps 1–16).
- Steps 8 and 9 dim **the whole screen** except the highlighted button (`Hud.Spotlight`, screenshot `x-index-step-spotlight.png`) for up to 120 s each.
- During a hatch reveal the HUD slides away (`HudController` "reveal mode").

**Blocking**
- Nothing is blocked:
  - the Shop (Robux) is reachable at step 1;
  - the cash boards work as soon as money exists;
  - you can walk into Riverbank or Canyon at any Speed (soft gates; observed at Speed 238 standing in the Canyon).
- Muted while the tutorial runs: quest-complete toasts (`RetentionService` lines 211 and 236, QuestsController line 818) and "you can afford" toasts (`UpgradeHintController` line 83).

**Rejoining**
- `inferStart` only acts on step 0 or the legacy markers. Otherwise the 1 Hz loop repairs the state:
  - Steps 2–4 without a carried egg → back to 1, or to 5 if an egg is on the plot (observed: a save left at step 4 rejoined at 1/16).
  - Step 5 with pets and no eggs → 6.
  - Steps 11–15 are re-derived from the egg state.
  - Ack steps restart their timer (`stepStarted` = rejoin time).
- Side effect: **walk pickups can be collected again on every rejoin** while at step 1. `collected` is per session (FirstWalkService line 75). Observed: Speed 22 → 38 on rejoin.

**Possible softlocks** (from code; none hit in play)
- **6th+ player in a server:** no plot (`PlotService.assign` warns "No free plot"). Step 3 ("roll onto your plot") and step 4 can never complete.
- **Step 1 when all 5 Meadow nest eggs are taken:** nests refill only on the Day reset (`EggService.RefillNests("Day")`), so the wait can be up to 5:00. Dropped or loose eggs can still be grabbed.
- **Step 16:** needs $5,000. With a normal Owl (~$85/s) that is ~1 min, so it is not a softlock.

**Idle help**
- Not found. TutorialController and TutorialService have no idle or forgiveness timer.
- The beam, chevrons and NestMarker are always on, so the target is always marked.

**Ending**
- Step 17 shows the toast "Tutorial complete! Get faster to reach deeper zones."
- Then the muted toasts (upgrade hints, quest complete) start flushing.

**Skipping**
- There is no skip button.
- Veterans are skipped automatically (`inferStart`): `WheelTier >= 1` → Done; pets → step 6; eggs → step 5; plus the three legacy rules.
- `DataService.Wiped` (admin wipe) restarts the tutorial.

---

## 4. Numbers to simulate the first 10 minutes

### Movement
- **Formula:** `WalkSpeed = min(16 + 10·log10(max(Speed,10)/10), 160)` (`Shared.Curves.WalkSpeedFromSpeed`, `Economy.SpeedCurve`).
  - Speed 10 → 16.00
  - Speed 36 → 21.56
  - Speed 300 → **30.77**
  - Speed 356 → 31.51
  - Speed 8,000 → **45.03**
- **Egg drag:** WalkSpeed × `max(1/(1+0.12·(size−1)), 0.4)`. Size 2 → 0.89, size 3 → 0.81.
  - Observed: walk 18.8 → 17.2 while the egg grew 1.26 → 2.1.
  - The carry card shows "−12% speed" / "−26% speed".
- **Speed sources:**
  - **Walk pickups:** `FirstWalkService.SPEED_PER_PICKUP = 2`. 13 pickups = +26 at step 1 only.
  - **Wheel:** `Economy.Wheel.BaseGainPerSecond 2 × tier Multiplier` (T1 ×1, T2 ×3, T3 ×8, T4 ×20) × `Multipliers.Treadmill` (rebirth × trail × live boost), ticked every 0.25 s (`SpeedService` TICK). Observed 46 → 57.5 in 6 s with T1.
  - **Index:** each newly discovered pet's `IndexReward.Speed` is claimed in the Index. Owl: **+320 Speed, +$1,600**. Capped below the second zone gate above current Speed (`IndexService` lines 6–13).
  - **Daily Day 1:** +max(60, 5% Speed) = **+60**.
  - **Quests:** +max(80, 4% Speed) × difficulty.
- **Gates** (soft; shown on the entrance sign, never enforced): Riverbank `RequiredSpeed 300`, Canyon `8000`.
- **Time to 300 Speed for a typical new player:** 10 + 26 (pickups) = 36 by the nest.
  - Wheel T1 (2/s) needs (300 − 36)/2 = **132 s** in the wheel.
  - **Or instantly** on claiming the Owl in the Index at step 8: 36 + 320 = 356.
  - If the player claims at step 8 (the tutorial asks them to), Riverbank opens around **minute 2–3**.
- **Canyon (8,000):** from ~400 Speed at T2 (6/s) it takes ~21 min; at T3 (16/s), ~8 min.
  - Index claims for Riverbank pets give 900–4,100 Speed each, so a few Riverbank hatches open it sooner.
  - Canyon is unlikely within 10 minutes.

### Distances (studs; seconds at WalkSpeed 16)

| Leg | Studs | s @16 |
|---|---|---|
| Plot1 spawn (188.25, 271.25) → Meadow nest centre (226.25, 96.25), straight | 179 | 11.2 |
| Plot1 spawn → safe line (z 176.25) | 95 | 5.9 |
| Nest → safe line | 80 | 5.0 (plus egg drag) |
| Safe line → Plot1 floor (z ≈ 285) | ~110 | ~6.9 (plus drag) |
| Plot → wheel board / WheelPad (porch, 167.75 / 176.25, ~272) | 10–20 | ~1 |
| Plot1 spawn → TrailStand (242.9, 203.75) | 87 | 5.4 |
| Shop | HUD button, no walk | — |
| Meadow safe line → Riverbank nest (−73.75) | 250 | 15.6 |
| Meadow safe line → Canyon nest (−303.75) | 480 | 30.0 |

Observed walk, plot spawn → nest: 8.2 s (Speed 22–38, collecting pickups).

### Meadow guard (`Config.Biomes.Meadow.Guard`)
- **Stats:**
  - "Mother Hen";
  - `WalkSpeed 13`, `WakeSeconds 1.2`, `HitReach 4`, `LeashStuds 0`;
  - behaviour `Hop` every 4.5 s: ×1.6 speed for 0.5 s (20.8 studs/s during the hop).
- **Wakes when** any egg is grabbed from its nest (`EggService.Grabbed`; GuardService states Sleep → Waking → Chase).
- **Can it catch a player walking straight home?**
  - **Observed, run 2** (Speed 22, walk 19.4, egg 1.26 → 2.1): the guard never came closer than **48 studs**. It woke at t = 0, chased from t = 1.6 s, and gave up when the player crossed the line at t = 4.3 s.
  - **Observed, run 1:** the player stood still for ~1 s after grabbing and was **caught in ~1.5 s** at the nest. The guard post is 16 studs from the nest centre.
  - At walk 16 with a size-2 egg (×0.89 → 14.2) the margin over the hen's 13 is small, and the Hop burst closes ~3.5 studs per hop. A player who hesitates, or walks the wrong way, is caught.
- **On a catch** (`GuardService.catch`, lines 415–431):
  - the egg is dropped as a physics ball (push 30, lift 28);
  - if the player was caught inside the guard's own biome, the egg returns to its nest after `RETURN_TO_NEST_DELAY 1.5 s`;
  - the player gets knockback (70 horizontal, 45 up);
  - `FeelController` plays an FOV punch, a white-red vignette, a 0.25 s camera ease and the "Hit" sound;
  - toast "Caught! Your egg went flying.";
  - `Stats.TimesCaught += 1`;
  - no money or Speed penalty;
  - the tutorial falls back to step 1.

### Eggs
- **Growth:** `GrowthPerStud` Meadow 0.018, Riverbank 0.015, Canyon 0.013. It applies to every stud walked while carrying, anywhere (EggService lines 853–889), × `Multipliers.Growth` (2x with the pass).
- **Decay:** × `exp(−3·(size − start)/(cap − start))` (`Economy.Egg.GrowthDecay 3`).
- **Cap:** each egg rolls `MaxSize × (0.45 + 1.3·u^2.4) × variant.CapMultiplier` (`Economy.Egg.Cap`). Meadow MaxSize 4; mean factor 0.83 → typical cap ~3.3.
- **Start size:** `[0.8, 1.3] × 1` + variant `StartBonus` (Plain 0 … Rainbow +1).
- **Luck:** `Biome.LuckBase × (1 + 0.75·(size−1)) × (1 + variant.LuckBonus) × Multipliers.Luck`. Meadow LuckBase is 5, so the shown "x" ≈ 5–13.
  - Drop odds use luck **relative** to LuckBase: weight × rel^min(tier−1, 4), with rel capped at 5.
  - Observed carry tags: x7.7, x11.1, x12.1, x17.8.
- **First-egg bonus:** `FirstEggSizeBonus 0.5` is added to the size at delivery (PenService line 514).
- **Size after rolling straight home (190 studs), computed pass-free:**

  | start / cap | at safe line | home | weight | luck | drag |
  |---|---|---|---|---|---|
  | 1.05 / 3.3 (typical) | 1.85 | **2.34** | 647 kg | x10.0 | 0.86 |
  | 0.80 / 1.8 (small) | — | 1.61 | — | — | — |
  | 1.30 / 7.0 (lucky) | — | 3.26 | — | — | — |

  Observed with the 2x pass: 1.08 → 2.76 (line) → 3.88 (plot), shown as "2,012 kg".
- **Display:** weight = 100 × size^2.2 kg. Physical diameter 2.5 × (1 + 0.9·ln size) studs, max 12.

### Hatching
- **Hatch time** = `Pet.HatchSeconds × min(size^0.8, 10)`. Meadow base times:

  | Pet | Base hatch |
  |---|---|
  | Chick | 3 s |
  | Bunny | 10 s |
  | Lamb | 20 s |
  | Fox | 35 s |
  | Owl | 60 s |
  | Golden Goose | 120 s |
  | Eggbert (Secret) | 180 s |

- **First egg:** forced to 5 s (`FirstEggHatchSeconds`); observed.
- **Second egg:** at least 40 s (`TutorialEggMinHatchSeconds`). Observed: 75 s (Marbled, size 2.56).
- **Hatch timer while carried:** keeps running at 1x. In your own wheel it runs at **3x** (T1); the formula is in belief 8.
- **Instant Hatch** (Robux) skip prompt on placed eggs: section 7.

### Income
- **Formula:** `$/s = BaseIncome × size^1.6 × Mutation.Multiplier (Silver 1.25, Golden 2, Rainbow 3) × Multipliers.Money` (rebirth 1 + 0.5·rebirths; 2x Money pass; 2x Money boost) (`Curves.PetIncome`).
- **Collection:** money arrives automatically; there is nothing to collect. Floating "+$X/s" pops appear over pets every 5–8 s (BaseFxController).
- **First Owl, pass-free:** size 2.1–3.8 → **$53–133/s**; typical size 2.84 → **$85/s**. Observed (2x passes): $172.2/s per Owl, $344.4/s total.
- **After the second egg:** it rolls normally. Typical outcomes at size ~2.2 (odds below): Chick ~$3.5/s, Lamb ~$14/s, Fox ~$28/s, Owl ~$56/s. The first Owl dominates.

### Prices a new player meets, cheapest first (time at ~$85/s pass-free)

| Item | Where | Price | Effect | Time |
|---|---|---|---|---|
| Wooden Wheel (T1) | plot Wheel board | $100 | 2 Speed/s while running; wheel hatch 3x | ~1 s |
| Pen L2 | plot Pen board | $300 | 6 slots (from 4), White Picket fence | ~4 s |
| Shell Wheel (T2) | Wheel board | $1,500 | 6 Speed/s | ~18 s |
| Pen L3 | Pen board | $3,000 | 8 slots, Stone Wall | ~35 s |
| Green Trail | lobby Trail Stand (cash) | $5,000 | ×1.5 wheel Speed gain | ~1 min |
| Speckled Wheel (T3) | Wheel board | $20,000 | 16 Speed/s | ~4 min |
| Pen L4 | Pen board | $30,000 | 10 slots | ~6 min |
| Blue Trail | Trail Stand | $75,000 | ×2 | ~15 min |
| Golden Wheel (T4) | Wheel board | $250,000 | 40 Speed/s | ~49 min |
| Rebirth #1 | Rebirth menu | $250,000 | resets Money, Speed, Wheel, Pen; +0.5 money and wheel multiplier | ~49 min |

- There are no cash items in the Shop: it is Robux-only (ShopController header).
- Speed Packs are Robux (section 7).
- The Index (+$1,600) and Daily Day 1 (≥ $250, or 2 min of income) add cash early.

### Other systems
- **Rebirth:** cost `250,000 × 3.5^rebirths`; bonus +0.5 per rebirth (`Config.Rebirth`).
- **Index:** one claim per discovered pet (`Pets.IndexReward`).
  - Meadow: Chick and Bunny 150 Speed / $500; Lamb 200 / $750; Fox 250 / $1,100; Owl 320 / $1,600; Golden Goose 400 / $2,300; Eggbert 1,200 / $6,900.
  - Riverbank: 900–4,100 Speed.
  - A zone's bat skin unlocks once the zone is complete.
- **Daily** (`Config.Retention.Daily`): 7-day cycle; Cash = max(Min, income × 60 × CashMinutes); Speed = max(SpeedMin, Speed × Pct).
  - Day 1: 2 min of income / $250 min, +60 Speed.
  - Day 3: +1 Trap. Day 5: Bat +1 tier. Day 7: 15 min of income, +400 Speed, 15 min 2x Luck.
  - Observed Day 1 with $344/s: "$41.0K +60 Speed".
- **Quests:** 3 per UTC day from the context at generation time.
  - Kinds and weights: RollKg 3, HatchRare 2, KnockLoose 2, Escape 3, Deliver 3, Hatch 3, Earn 3, Fuse 2.
  - Earn needs income > 0; Fuse needs ≥ 6 pets.
  - Reward cash = Nice(max(300, income × 240) × difficulty); Speed = Nice(max(80, Speed × 4%) × difficulty).
  - For a brand-new player (income 0, Speed ≤ 36) each reward is **$300–450 and 80–120 Speed** (difficulty 1–1.5). Escape also gives +1 Trap.
  - **The exact first 3 quests were not captured** (Quests menu not opened).

### Timers
- **Egg reset:** every 300 s (290 day + 10 night).
  - HUD "NEW EGGS IN m:ss" → "NIGHT · NEW EGGS IN 0:0x". Night darkens the lighting (EventController).
  - At **night start** (`EggService` lines 1026–1042), anyone in the corridor (Meadow included) loses a carried egg: "Night fell! Your egg rolled back to its nest." They are also **teleported home**; observed in run 1 even without an egg (`x-night-sent-home.png`).
  - Grabbing is refused at night (EggService line 580).
  - At day start every nest refills and loose/ground eggs are cleared.
  - The Hint "Night resets the field…" shows when night is < 25 s away.
- **During the first steal:** a player who joins with ~1–2 min left will likely meet a night mid-walk. Their first egg is lost and they're sent home.
- **Live events** (`EventService` lines 41–48): first Capture the Egg at 8 min, then every 20 min; first Egg Golem boss at 14 min, then every 30 min; first Night Run at 25 min, then every 45 min (3 min long). HUGE EGG every 3 resets (~15 min).

### Rarity chances (Meadow, computed from `Curves.LuckAdjustedTable`)

| Size (rel. luck) | Chick | Bunny | Lamb | Fox | Owl | Golden Goose | Eggbert |
|---|---|---|---|---|---|---|---|
| 1.0 (1.00) | 40.0% | 28.0% | 18.0% | 9.0% | 4.0% | 1.0% | 0.05% |
| 1.5 (1.38) | 32.3 | 22.6 | 20.0 | 13.7 | 8.4 | 2.9 | 0.14 |
| 2.2 (1.90) | 22.8 | 15.9 | 19.5 | 18.5 | 15.6 | 7.4 | 0.37 |
| 3.0 (2.50) | 14.7 | 10.3 | 16.5 | 20.6 | 22.9 | 14.3 | 0.72 |

- **Egg variants per nest egg** (`Config.EggVariants` weights /100): Plain 44, Speckled 22, Spotted 14, Marbled 10, Shiny 6, **Golden 3, Rainbow 1**.
- **Size tiers** (size/cap ratio): Big ≥ 0.6, Huge ≥ 0.85, Colossal ≥ 0.999 (`Config.SizeTiers`).
  - A typical straight-home egg reaches ~0.7 (BIG).
  - Huge is unlikely without wheel growth; Colossal only from the HUGE EGG event.
- **Mutations:** Silver ×1.25, Golden ×2, Rainbow ×3. **Chances not found** in `Config.Mutations` Source (only Id and Multiplier fields); not traced in PenService.
- **Rough chance of something "rare" in 10 minutes** (≈5–8 eggs, computed): at least one Golden or Rainbow-variant egg ≈ 1 − 0.96⁶ ≈ **22%**; at least one Golden Goose ≈ **35–45%** at sizes 2–3.

---

## 5. The core loop, felt from the player's side

**Grabbing the egg**
- Prompt: `Workspace.World.Eggs.Egg_Meadow.ProximityPrompt`, custom style (`CustomPrompts`).
  - Action "Steal"; object e.g. "Marbled Meadow Egg".
  - Key E; gamepad ButtonX.
  - **Hold 0.6 s** (`Prompts.StealHold`).
  - MaxActivationDistance ~9.4–9.7; RequiresLineOfSight false.
- Server reach: 12 + egg radius, plus a line-of-sight raycast and a burst limit ("Slow down!").
- The egg becomes a rolled ball in front of the player (CarryController). Its rotation is integrated from movement. Jumping is disabled.
- Carry card (`Hud` carry card, screenshot `08`): e.g. "Marbled Meadow Egg · 2,012 kg / x17.8 · −26% speed · growth 23% / Click your plot to place it", plus a red "Drop [Q]" button.
- Sound: "Grab" (CarryController line 336). The egg label pulses every half-turn, pops on growth, and plays "Grow".
- **Observation:** with 5 eggs close together, the prompt shown on screen was for a different egg (Spotted) than the nearest one (Marbled). Twice in run 1, holding E did nothing; a scripted `InputHoldBegin` on the nearest prompt worked.

**Guard wakes**
- "!" alert and the "GuardWake" sound (GuardFxController line 306). Zzz stop.
- HUD chase mode: fade and a "RUN!!" banner.
- A pulsing ground arrow points home. Chase music (MusicChase).
- The "StealAlarm" sound exists in Assets; where it is used was not traced.

**The chase**
- Observed: the hen stayed ≥ 48 studs back for a player who runs at once (walk 19.4).
- Hop telegraph: the guard flashes yellow, then red (Action/ActionAt attrs).
- Catch beat: see the Meadow guard section.

**Crossing the safe line**
- `CarrySafe` flips on. CarryController shows a **gold burst + ring + rising gold "curtain"** (`CURTAIN_SECONDS 0.8`) and plays "LockIn".
- The line pulses while you carry outside it. The guard returns.
- Toast "You escaped the Mother Hen!" (GuardService line 411). The card switches to "Safe!".
- There is no big "SAFE!" banner text beyond that.

**Rolling home and growth feedback**
- The weight on the carry card and billboard ticks up, with a "Grow" sound and pops.
- The growth-% line fades from bright to dull as growth slows. The clover luck tag updates.
- There are no milestone pops ("x2!" etc.); none were found.
- The HeavyEgg hint appears once at ≤ 85% speed.

**Placing on the plot**
- No prompt: a ghost egg follows the mouse/tap over the floor (green/red). Click, tap or R2 places it (PlacementController).
- Drop-in (PlotEggController): it falls from the sky with a fade (`DROP_SECONDS 0.7`) and squashes with a dust ring (0.22 s).
- Then a big clover luck tag counts up from x1.00 (`TICK_SECONDS 1.3`), shown as a large "x17.8" in screenshot `09`. A countdown billboard shows "0:02".

**Hatch reveal** (PlotEggController lines 46–51, HudController lines 734–738)
- Timeline:
  1. hold E 0.5 s (`HatchHold`);
  2. the egg shakes left-right-left;
  3. it cracks into shards;
  4. the pet pops up (0.46 s), hovers (0.25 s), and flies along a bezier into the owner (0.7 s); the whole sequence is ~2.25–2.4 s;
  5. the HUD slides away and the **hatch card** shows for **2.9 s + 0.3 s fade** (1.8 s when more are queued);
  6. then the "New discovery: Owl! Claim it in the Index" toast.
- Rare hatches play "HatchRare" (HudController line 1127). The first hatch adds "Mama!" + confetti + HatchRare.
- **Total ≈ 6 s.** The card disappeared before a screenshot could land: `10-hatch-reveal.png` shows the pet flying out, and `x-hatch-crack.png` the cracked shell.
- **Whether the Epic Owl is staged as special:** beyond the rarity colour (Epic purple) and the first-hatch Mama bubble, there is no extra beat for an Epic.

**First income**
- The HUD "+$X/s" line updates and the cash counter climbs: $0 → $1.7K in ~5 s observed (2x).
- Floating "+$X/s" pops appear over the pet (`BaseFxController`, every 5–8 s, distance-culled at 70).
- An over-pet billboard shows "Owl 2,623 kg / $172.2/s".
- Cash arrives automatically.

**First purchase (wheel)**
- The wheel board ("HAMSTER WHEEL / Buy a hamster wheel / +0 > +2 Speed/s / $100") is a SurfaceGui moved into PlayerGui.
- On buying, the wheel falls out of the sky (`DROP_SECONDS 0.8`), slams down with a dust ring and bounce (0.35 s), the camera shakes a little, and "WheelLand" plays.
- Toast "Bought the Wooden Wheel! Run inside it to gain Speed." (`x-wheel-dropped.png`).

**Moments with no sound or VFX found**
- Crossing the safe line has VFX and LockIn, but no unique "safe" jingle.
- Size milestones: none.
- Tutorial completion: toast only.
- Index claim: SpeedFx flies "+N" into the counter and plays "SpeedGain"; no dedicated jingle found.

---

## 6. Goals and reasons to stay

**Visible next goal per minute** (pass-free estimate from the step flow; observed steps in brackets)

| Minute | On screen | Progress shown? |
|---|---|---|
| 0–0:15 | "Steal an egg!" + "Meadow nest: N studs" + beam + pickups | distance counter ✔ |
| 0:15–0:35 | "Roll it home!" → "Safe!" → "Place your egg" | carry card weight/luck ✔ |
| 0:35–0:50 | "Hatch it!" (5 s timer) → Owl → "Place your pet" | egg countdown ✔ |
| 0:50–1:00 | "Get faster: save $100…" | none: no "$x / $100" ticker; money is instantly enough |
| 1:00–1:30 | "Run in your hamster wheel!" (3 s) → "Collect them all" (spotlight) | — |
| 1:30–3:00 | "Daily rewards" (spotlight) → "You're protected" (9 s) | — |
| 3–7 | "Steal a second egg" → Place → Pick up → Roll in wheel (40–75 s timer) → Hatch | egg timer ✔ |
| 7–8 | "Get a trail": Green Trail $5,000 | `SaveFor`: the card shows $ progress ✔ |
| 8–10 | Tutorial done toast. No card. Next goals: upgrade-hint toasts (now unmuted), Quests badge, Riverbank sign "300 Speed" | upgrade toasts |

**Social proof**
- Visible from spawn: lobby leaderboards (Biggest Egg, Top Earners, Most Rebirths) and the BIGGEST EGG TODAY board with a 3D holder (observed showing the test account, "40,923 kg").
- Other players' pets wander in their pens. On touch devices, "Hide other players' pets" defaults to on (`BaseFxController`).
- Server announcements for Legendary+, and for HUGE/Colossal (`Rarities.Announce`, `SizeTiers.Announce`).

**Upcoming events visible to a new player**
- HUGE EGG countdown on the arch, "HUGE EGG IN 14:48" at join, ~15 min.
- Event banners for Capture (8 min), Boss (14 min) and Night Run (25 min) appear when they start (`Events.Banners`). No countdowns before then were found.
- Admin Abuse: not found as a scheduled event; `AdminService` has admin boosts and events.

**Leaving**
- Offline earnings: `Economy.OfflineIncomeFraction 0.5`, capped at `OfflineMaxSeconds` 8 h. Where they are shown on return was not traced.
- No "come back" prompt on leave or menu open was found.

---

## 7. Monetisation in the first 10 minutes

**Auto-shown offers:** none. grep finds no automatic `PromptProductPurchase` or `PromptRobux` call on join or at a tutorial step.

**Where Robux offers appear**

| Offer | Where | When | Price |
|---|---|---|---|
| Starter "2x Speed (15 min)" | Shop, top row, gold, "ONLY 3 R$!" | any time (Shop button from second 0) | 3 R$ |
| Instant Hatch | `SkipPrompt` on each placed egg: "Skip R$ 9" / "Hatch now", key R, hold 0.25 s, right next to Hatch (E) and Roll (F) | the 2nd egg (step 12, minute ~3–5) | 9 / 49 / 149 / 299 R$ by time left |
| Trails for Robux | `RobuxPrompt` on each Trail Stand pedestal, next to the cash `BuyPrompt` | step 16 | 19 / 29 / 49 / 79 / 119 / 199 R$ |

**Product list** (`Config.Products`; id, name, price hint)

| Key | Kind | Id | Name | R$ |
|---|---|---|---|---|
| Starter2xSpeed | Product, Once | 3718310747 | 2x Speed (15 min) | 3 |
| Pass2xMoney | Pass | 2034464306 | 2x Money (forever) | 399 |
| Pass2xGrowth | Pass | 2034170312 | 2x Egg Growth (forever) | 467 |
| Luck15 / Luck30 / Luck60 | Product | 3718311087 / …100 / …115 | 2x Luck 15 min / 30 min / 1 h | 49 / 99 / 199 |
| Money30 | Product | 3718311148 | 2x Money (30 min) | 99 |
| Speed60 | Product | 3718311160 | 2x Speed (1 hour) | 129 |
| SpeedS / SpeedM / SpeedL | Product | 3718311169 / …189 / …203 | Speed Pack S / M / L: +max(Base 50/150/400, zone span × 25% / 75% / 200%) | 49 / 129 / 349 |
| HatchT1–T4 | Product | 3718311233 / …248 / …263 / …275 | Instant Hatch (≤ 2 min / 10 min / 30 min / any) | 9 / 49 / 149 / 299 |
| Traps5 | Product | **0 (not set)** | +5 Traps | 25 |
| Trail_Green … Trail_Rainbow | Product | 3718311297 … 3718311399 | trail tier 1–6 | 19 / 29 / 49 / 79 / 119 / 199 |

**Blocking:** no purchase is blocked during the tutorial.

**Mis-tap risk**
- The Shop never sells cash items, and every Robux button opens an in-modal confirm first ("Buy X for R$ N?") (ShopController header).
- In the world:
  - the egg's **Skip (R, 0.25 s)** prompt sits beside **Hatch (E, 0.5 s)** and **Roll (F, 0.4 s)**;
  - each trail pedestal's **Robux prompt** sits beside its cash **Buy** prompt.
- Both open Roblox's own purchase dialog, so no purchase completes without a second confirm. On a phone, though, these are adjacent tap targets.

---

## 8. Multiplayer effects on a new player

**Server size vs plots**
- `Players.MaxPlayers` reads **60** in Edit. Confirm this in Game Settings: it is the place setting.
- `Workspace.World.Plots` has **5** plots (Plot1–3 back row, Plot4 and Plot5 sideways).
- **Assignment** is first-free by index (`PlotService.assign`).
- **All plots taken:** `warn("[PlotService] No free plot …")`. The 6th+ player keeps the LobbySpawn, has no plot, and **cannot place eggs**, so the tutorial stalls at step 3/4.
- **This is the largest structural risk if MaxPlayers really is 60.**

**Interference in the first 10 minutes**
- **Shared nests:** 5 eggs per nest, refilled only at the Day reset. Other players can empty the Meadow nest and leave a newcomer waiting up to 5:00.
- **Dropped or loose eggs** can be grabbed by anyone (GroundHold 0.3 s).
- **Bats and traps** (`PvPService`): bat knockback 60 and a 0.6 s stun knock eggs loose; traps root for 5 s.
- **Protection:** new players are protected for `PROTECT_SECONDS = 600` (10 min) **and** for as long as they have never hatched an egg (`IsProtected`: `os.time() < ProtectedUntil or next(data.Index) == nil`).
  - Protected players can't be batted or trapped, and can't bat or trap.
  - HUD: "PROTECTED m:ss", then "PROTECTED - hatch your first egg".
- **Plot eggs** can't be stolen ("Eggs placed on a plot are never world eggs").
- **Guards** are shared, one per biome. A newcomer can be caught by a guard another player woke.

---

## 9. Devices and input

**Prompts** (read live in play)

| Prompt | Key / pad | HoldDuration | MaxActivationDistance | RequiresLineOfSight |
|---|---|---|---|---|
| Nest egg "Steal" | E / ButtonX | 0.6 | 9.35–9.70 (varies with egg size) | false |
| Placed egg "Hatch" | E | 0.5 | 10 | false |
| Placed egg "Roll / Pick up" | F | 0.4 | 10 | false |
| Placed egg "Skip R$ 9 / Hatch now" | R | 0.25 | 10 | false |

- The wheel has no prompt: standing on `WheelPad` sets `OnWheel` (SpeedService).
- Placement is a click/tap/R2 on the floor.
- Pets are placed with Tool.Activated (click/tap/R2).

**ClickDetectors:** only the Egg Golem boss (`BossService` line 440). It also has a `RequestBossHit` path. No tutorial step needs one.

**Gamepad**
- Steal/Hatch/Roll prompts have gamepad keys, and placement accepts R2.
- **The plot boards (wheel/pen buy) are SurfaceGui buttons moved into PlayerGui.** Gamepad selection of SurfaceGui buttons was **not verified**.
- Mouse clicks through MCP on the board's Buy button did not register in this test. The purchase was done by firing `RequestBuy("Wheel", 1)`, which is the remote the button calls.
- Steps 6 and 7 on gamepad: unverified.

**Phone**
- Not emulated (no Device Simulator through MCP).
- Prompt hold times of 0.25–0.6 s are phone-friendly.
- The HUD side buttons are ~110×50 px on PC at this resolution.
- Tutorial card text: see the computed size in section 2. It is likely the smallest text on screen.
- **Overlaps seen on PC:**
  - the TIP pill covers "Meadow nest: N studs" (`01`);
  - the tutorial card covers the over-pet "$/s" billboard (`16`);
  - the "2x CASH · 2x GROW" pill sits under the card's lower edge (pass owners only).

---

## 10. Errors and performance

**Console** (both playtests, after filtering)
- 33 lines of "The experience doesn't have access permission to use asset id …" and "Failed to load sound … not authorized", repeated per session (90 on the server).
  - **All 33 ids are Sounds inside `ServerStorage.REFERENCE_PLEASEUSE`** (e.g. `…From_ReplicatedStorage.Assets.Sprinklers.PlaceSFX`, `…Models.Knife.Handle.StabSound`). That is a consult-only reference folder.
  - **No game sound is affected:** all 28 `Shared.Assets.Sounds` loaded.
  - Players never load ServerStorage, but the errors spam Studio output.
- 6× "Failed to upload TexturePack (… DependenciesPermissionDenied) PBR textures may not be visible in game." Source asset not traced.
- 1× "Unable to load plugin icon …4725619926": a Studio plugin, not the game.
- 3× "Infinite yield possible on 'Remotes:WaitForChild("RequestWalkPickup")'" from `FirstWalkController` line 123 → `Shared.Net` line 96.
  - These appeared in run 1 **after** my tester artifact; they did not appear in run 2.
  - Speed pickups were still granted in both runs.
- No other errors or warnings from game scripts. `[Server] Started 31 services`.

**Performance**
- Workspace descendants: **13,161**. Whole DataModel: 54,409 (Edit).
- `Workspace.StreamingEnabled = true`. `StreamingTargetRadius`, `StreamingMinRadius` and `StreamingIntegrityMode` are not readable from the plugin context here (not found).
- **Per-frame or fast loops seen in client headers:**
  - `HudController` re-stacks Tutorial → Banners → HintPill → Toasts **every frame**.
  - `FirstWalkController` chevrons at 30 Hz, pickups bob and spin (step 1 only).
  - `GuardFxController` animates limbs from Body motion.
  - `PetIdleController` runs a 4 Hz scheduler plus per-frame updates only for pets mid-trick.
  - `EventController` uses one Heartbeat, only while something is visible.
  - `TrailStandController` runs orb loops within a cull distance.
  - `SpeedFxController` uses popup tweens.
- **Server loops:** `EggService` carry Heartbeat, `GuardService` per-frame CFrame stepping per awake guard, `SpeedService` at 0.25 s, `TutorialService` and `OnboardingService` at 1 Hz / 2 Hz.

---

## 11. Analytics

`AnalyticsService` **is used**: only in `ServerScriptService.Server.Services.OnboardingService` (line 49).

```lua
local ok, err = pcall(AnalyticsService.LogOnboardingFunnelStepEvent, AnalyticsService, player, nextStep, STEPS[nextStep])
```

- It only logs for players decided "new" on their first load: `TutorialStep <= 1`, no delivered eggs, no pets, no eggs. The decision is saved in `data.Funnel`.
- Steps are logged once each and in order; skipped steps are filled in.
- **Funnel steps:**
  1. `Joined`
  2. `LeftSpawn` (≥ 25 studs from LobbySpawn: note players spawn on their plot, 15 studs away)
  3. `EnteredMeadow` (`WorldIndex.InCorridor`)
  4. `ReachedNest` (≤ 22 studs from the Meadow nest centre)
  5. `Tut2_Escape`, 6 `Tut3_Home`, 7 `Tut4_Place`, 8 `Tut5_Hatch`, 9 `Tut6_BuyWheel`, 10 `Tut7_Wheel`, 11 `Tut8_Index`, 12 `Tut9_Daily`, 13 `Tut10_Protection`, 14 `Tut11_Steal2`, 15 `Tut12_Place2`, 16 `Tut13_PickUp`, 17 `Tut14_WheelRoll`, 18 `Tut15_Hatch2`, 19 `Tut16_Trail`, 20 `Tut17_Done`
- **Hook points:** walk steps poll at 2 Hz; tutorial steps fire on `TutorialService.StepChanged` (fired in `setStep`).
- Observed: 12 "LogOnboardingFunnelStepEvent event fired" lines across the two runs.
- **Not found:** `LogProgressionEvent`, `LogEconomyEvent`, `LogCustomEvent`. Sinks and sources (wheel, trail and pen purchases; Robux) and the event joins have no analytics.

---

## 12. Screenshots (`handoffs/ftue-screenshots/`)

| File | Source | Notes |
|---|---|---|
| `01-spawn-view-default-camera.png` | play, t ≈ 11 s, fresh save | plot-porch spawn; TIP pill over the distance line |
| `02-spawn-view-phone.png` | **not captured** | no Device Simulator through MCP |
| `03-lobby-top-down.png` | Edit | lobby, arch, EggStatue, 5 plots, boards |
| `04-path-spawn-to-meadow-nest.png` | Edit | pickups + chevrons through the arch |
| `05-meadow-nest-and-guard.png` | Edit | nest (eggs spawn only in play) + Mother Hen + beacon |
| `06-safe-zone-line.png` | Edit | "SAFE ZONE" gold line, pickups beyond |
| `07-tutorial-card-step1.png` | same frame as 01 | the step-1 card and beam |
| `08-carried-egg-billboard.png` | play | carry card on the plot, step 4/16 |
| `09-plot-with-egg-incubating.png` | play | placed egg, luck tag x17.8, 0:02 countdown |
| `10-hatch-reveal.png` | play | pet flying out of the egg; **the hatch card itself was not caught** (visible for 3.2 s) |
| `11-hud-full-phone.png` | **not captured** | no Device Simulator through MCP |
| `12-shop-open.png` | play | Starter Offer, passes (shown "Owned" for the test account) |
| `13-index-open.png` | play | Index with the "Claim your reward!" tip, step 8 |
| `14-riverbank-guard.png` | play | Riverbank nest, croc asleep behind |
| `15-canyon-guard.png` | play | Canyon nest, scorpion behind |
| `16-pets-in-pen.png` | play | Owl in the pen; the card covers its $/s tag |

Extras (`x-*.png`):
- `x-walk-pickups-playtest`
- `x-caught-at-nest`
- `x-night-sent-home`
- `x-hatch-crack`
- `x-first-pet-placed` ($344.4/s, 2x passes)
- `x-wheel-dropped`
- `x-index-step-spotlight` (whole screen dimmed)
- `x-daily-open`
- `x-step16-trail`

The test account's Roblox username appears on the Biggest Egg board in several screenshots.

---

## 13. My own read (opinion)

**Five biggest problems for a 9-year-old on a phone**
1. **Standing still at the nest gets you caught in ~1.5 s, and the first catch costs the egg.** The hen sleeps 16 studs from the nest and wakes 1.2 s after the grab. A child who grabs and then looks at the screen loses the egg on their very first try, with a knockback and a red flash.
2. **Text-heavy cards that are small on a phone.**
   - Step 2 is 26 words and tells them to "Kite the Mother Hen for more", which the code no longer rewards.
   - At ~10 s a PvP tip about traps covers the one useful line (nest distance).
   - The compact card is ~38 px tall on a phone.
3. **The economy pacing collapses after the first hatch.**
   - The Owl pays ~$85/s, so "Save $100" is instant and the wheel/pen boards are affordable at once. There's no "save up" tension.
   - Then the long middle comes: two spotlight steps that dim the whole screen for up to 2 minutes each, and a 4-minute second-egg sequence (steal → place → pick up → wheel → hatch) that teaches a niche mechanic before the player has a real reason to care.
4. **Interruptions that undo progress without warning the newcomer.** A night at 5:00 teleports them home and deletes the carried egg. Nests empty by other players stay empty until the reset. With 5 plots and (if real) 60 MaxPlayers, a 6th player can't finish step 3 at all.
5. **The plot boards are the only way to buy the wheel, and they're small world signs.** The child must find a board outside the plot. On a phone, tapping a SurfaceGui sign is fiddly, and it was unverified on gamepad.

**Five best moments the game already has**
1. **The steal-and-run:** a 0.6 s hold, the hen's "!" and roar, chase music, a "RUN!!" banner and the gold safe line with a curtain burst. It's a clear, physical mini-drama in the first 15 seconds.
2. **The egg literally grows as you roll it:** weight ticking up, a clover luck tag and a rolling ball that gets visibly bigger. It's a great "my thing is getting better because I moved" loop.
3. **The drop-in and luck count-up on placing:** the egg falls from the sky and squashes, and a big clover "x17.8" counts up. It's satisfying and readable.
4. **The wheel falling out of the sky** with dust and a camera shake, on the first purchase.
5. **Speed gains that fly into the counter** ("+N" bezier into the shoe icon), plus the walk pickups. Every early action produces a visible number going up, and the walk has content.
