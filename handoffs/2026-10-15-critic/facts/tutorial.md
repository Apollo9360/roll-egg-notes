# Tutorial, onboarding and hints (read-only snapshot)

Sources: `ReplicatedStorage.Shared.Config.Tutorial` (95 lines, "Cfg"), `ServerScriptService.Server.Services.TutorialService` (232, "TS"), `OnboardingService` (119, "OB"), `SettingsService` (116, "SS"), client `TutorialController` (781, "TC") and `HintController` (239, "HC"), all under `StarterPlayer.StarterPlayerScripts.Client.Controllers`. Line numbers are file lines.

## 1. State model

* Progress lives in `data.TutorialStep` (DataService TEMPLATE `TutorialStep = 0`, DataService:91), replicated to the owner through `DataChanged` (key is in REPLICATED, DataService:110). Persisted by ProfileStore with the rest of `profile.Data`.
* Step 0 = not started. Steps 1..16 are `Cfg.Steps` indices (16 steps). `Tutorial.Done = #Steps + 1 = 17` (Cfg:62). `Tutorial.InProgress(step) = (step or 0) < 17` (Cfg:70-72).
* Constants: `WheelSecondsToFinish = 3` (Cfg:63), `FirstAckStep = 8` (Cfg:64), `LegacyDone = 8` (:65), `LegacyDone2 = 11` (:66), `LegacyDone3 = 16` (:67).
* Server code names: `STEAL..WHEEL = 1..7` hard-coded (TS:29); 11-16 resolved with `Tutorial.IndexOf("Steal2"/"Place2"/"PickUp"/"WheelRoll"/"Hatch2"/"Trail")` (TS:31-36). Steps 1-10 are therefore index-coupled to the config order ("only ever APPEND steps", Cfg:3).
* `setStep` (TS:52-61): if the step differs, stamps `stepStarted`, `DataService.Set(player, "TutorialStep", step)`, fires `StepChanged(player, step)`, and when `step == DONE` fires `Notify "Tutorial complete! Get faster to reach deeper zones." "Success"`.
* `advance(player, from, to)` (TS:64-69): moves only if the current step is in `from`.

## 2. Steps (Config.Tutorial.Steps)

`{Action}` in Text becomes "Tap" on touch-only devices, else "Click" (TC:91-93, replaced in TC:214). Highlight/Ack/Pre/SaveFor columns are empty ("-") where the config has none.

| # | Id (Cfg line) | Title | Text (full) | Target | Highlight | Ack | Pre | SaveFor |
|---|---|---|---|---|---|---|---|---|
| 1 | Steal (:37) | Steal an egg! | Sneak up to the Meadow nest and hold to steal an egg. | NestEgg | - | - | - | - |
| 2 | Escape (:38) | Roll it home! | Your egg grows while you roll it in its zone. Kite the Mother Hen for more, then cross the gold line into the bases: guards stop there. | LockLine | - | - | - | - |
| 3 | Home (:39) | Safe! | Roll your egg onto your plot. It keeps growing with every step you take! | Plot | - | - | - | - |
| 4 | Place (:40) | Place your egg | {Action} anywhere on your plot to set the egg down. | nil (no beam) | - | - | - | - |
| 5 | Hatch (:41) | Hatch it! | When the timer says READY, hold on the egg to hatch it. | PlacedEgg | - | - | - | - |
| 6 | BuyWheel (:42-45) | Get faster | Your pet earns cash. Save $100 and buy your first hamster wheel on the Wheel board outside your plot. | WheelBoard | - | - | `Until="PetPlaced"`, Title "Place your pet", Text "Pets only earn cash in your pen: equip your new pet from the hotbar, then {Action} inside your pen.", Target Pen | - |
| 7 | Wheel (:46) | Run in your hamster wheel! | Run inside the hamster wheel outside your plot to gain Speed. More Speed = deeper zones = bigger eggs. | Wheel | - | - | - | - |
| 8 | Index (:49) | Collect them all | Open the Index: every new pet you discover pays Speed and cash. Claim your reward, then close it! | - | Index | `CloseModal:Index`, AckSeconds 120 | - | - |
| 9 | Daily (:50) | Daily rewards | Come back every day: open Daily and claim today's free reward. | - | Daily | `CloseModal:Daily`, AckSeconds 120 | - | - |
| 10 | Protection (:51) | You're protected (for now) | New players can't be batted or trapped. When your shield ends, watch out for bats and traps in the wild zones! | - | - | `Timer`, AckSeconds 9 | - | - |
| 11 | Steal2 (:53) | Steal a second egg | Grab another egg from a nest and roll it home. Your hamster wheel will help it hatch! | NestEgg | - | - | - | - |
| 12 | Place2 (:54) | Place it | Roll it onto your plot and {Action} a spot to set it down. | Plot | - | - | - | - |
| 13 | PickUp (:55) | Pick it back up | Placed eggs can roll again! Hold F on your new egg (or tap Roll) to pick it up. | PlacedEgg | - | - | - | - |
| 14 | WheelRoll (:56) | Roll it in your wheel | Run in your hamster wheel with the egg: its hatch timer drops 3x faster! | Wheel | - | - | - | - |
| 15 | Hatch2 (:57) | Hatch it! | Timer done! {Action} your plot to set the egg down, then hold E on it to hatch. | HatchEgg | - | - | - | - |
| 16 | Trail (:59) | Get a trail | Buy the Green Trail at the Trail Stand in the lobby. Trails multiply the Speed your hamster wheel gives! (Text is a template literal: `Trails.List[1].DisplayName`) | TrailStand | - | - | - | 5,000 (`Trails.List[1].Price`, Config.Trails:23) |
| 17 | (Done) | n/a | No step 17 exists in `Steps`: `Tutorial.Done` | | | | | |

Text/number consistency checks: "Save $100" matches `Economy.Wheel.Tiers[1].Price = 100` (Economy:96). "3x faster" matches `WheelHatchBonus = 2` (1 + 2 = 3x at tier 1, Economy:74). Protection text vs `PROTECT_SECONDS = 600` (PvPService:63).

## 3. Completion conditions and fall-back rules (server)

All quoted from TS. Signals come from `EggService` (Grabbed, ReachedSafety, EnteredPlot, Delivered) and `PenService.Hatched`.

| From step | To | Condition (code, line) |
|---|---|---|
| 1 Steal | 2 Escape (or 3 Home if already safe) | `EggService.Grabbed:Connect(... advance(player, { STEAL }, if EggService.IsSafe(player) then HOME else ESCAPE))` TS:169-171 |
| 1 or 2 | 3 Home | `EggService.ReachedSafety:Connect(... advance(player, { STEAL, ESCAPE }, HOME))` TS:172-174 (crossing the lock line, EggService.zoneTick :766-776) |
| 3 Home | 4 Place | `EggService.EnteredPlot:Connect(... advance(player, { HOME }, PLACE))` TS:175-177 (fired once per carry when the carried egg enters your plot, EggService:857-860) |
| 1, 2, 3, 4 | 5 Hatch | `EggService.Delivered:Connect(... advance(player, { STEAL, ESCAPE, HOME, PLACE }, HATCH))` TS:178-180 (egg placed and accepted by `PenService.acceptDelivery`) |
| 5 Hatch | 6 BuyWheel | `PenService.Hatched:Connect(... advance(player, { HATCH }, BUY_WHEEL)` TS:181-183. Also 1 Hz fallback TS:201-202: `step == HATCH and #data.Eggs == 0 and #data.Pets > 0` |
| 6 BuyWheel | 7 Wheel | 1 Hz TS:203-204: `step == BUY_WHEEL and data.WheelTier >= 1` |
| 7 Wheel | 8 Index | TS:205-211: `if player:GetAttribute("OnWheel") then wheelSeconds[player] = (wheelSeconds[player] or 0) + 1; if wheelSeconds[player] >= Tutorial.WheelSecondsToFinish then setStep(player, Tutorial.FirstAckStep)`. Cumulative 3 s on the wheel (counter is not reset when the player steps off; cleared on PlayerRemoving/Wipe) |
| 8, 9, 10 | step + 1 | Client ack: `TutorialService.Ack` TS:101-108 requires `current == step`, `step >= FirstAckStep`, `Steps[step].Ack` set, then `setStep(player, step + 1)`. Server fallback TS:218-225: `if os.clock() - started >= (def.AckSeconds or 30) + 2 then setStep(player, step + 1)` (so 122 s, 122 s, 11 s) |
| 11..15 | see below | 1 Hz `elseif step >= STEAL2 and step <= HATCH2 then secondEgg(player, data, step)` TS:212-213 |
| 16 Trail | 17 Done | TS:214-217: `elseif step == TRAIL then if ownsTrail(data) then setStep(player, DONE)`; `ownsTrail` = `type(trails)=="table" and (tonumber(trails.Owned) or 0) >= 1` (TS:38-41) |

`secondEgg` (TS:111-154) inputs: `carrying = EggService.IsCarrying(player)`, `hatchLeft = EggService.CarriedHatchLeft(player)` (non-nil = rolling a picked-up egg), and a scan of `data.Eggs` for `waiting` (placed, timer not done) / `ready` (placed, `os.time() - PlacedAt + Bonus >= HatchSeconds`).

| Step | Rule (TS line) |
|---|---|
| any of 11-15 with `hatchLeft` | TS:124-125 `setStep(player, if hatchLeft <= 0 then HATCH2 elseif step == HATCH2 then HATCH2 else WHEEL_ROLL)` |
| 11 Steal2 | `if carrying then PLACE2 elseif waiting then PICKUP` (TS:126-131) |
| 12 Place2 | `if not carrying then setStep(if waiting then PICKUP else STEAL2)` (placed it / lost it) (TS:132-135) |
| 13 PickUp, 14 WheelRoll | carrying a world egg: return (no change); `WheelRoll and ready and not waiting` -> HATCH2; `waiting` -> PICKUP; `ready` -> HATCH2; else STEAL2 (TS:136-148) |
| 15 Hatch2 | `if not carrying and #data.Eggs == 0 then setStep(player, TRAIL)` (TS:149-152), also `PenService.Hatched` -> `advance(player, { HATCH2 }, TRAIL)` TS:183 |

Fall-back (regress) rules in the 1 Hz loop (TS:190-228):
* TS:199-200: `(step == ESCAPE or step == HOME or step == PLACE) and not EggService.IsCarrying(player)` then `setStep(player, if #data.Eggs > 0 then HATCH else STEAL)` ("egg lost: caught, night, dropped").
* Steps 1 and 5-7 never regress apart from the forward rules above; steps 11-15 regress/jump per `secondEgg` (e.g. lose the egg: 12 -> 11).
* Info steps never block (auto-advance after AckSeconds + 2).

## 4. Rewards and gameplay effects tied to the tutorial

No step pays cash/items directly. Tutorial-related grants and tweaks that exist:
* Step 1 first-walk pickups: 13 pickups x `SPEED_PER_PICKUP = 2` = +26 Speed (FirstWalkService:18, gated `TutorialStep == 1`, FirstWalkService:21,47).
* Starter egg (not a tutorial-state check, but `data.Stats.EggsDelivered == 0`, PenService:510-521): first delivered Meadow egg always hatches an Owl, no mutation/tier, `+0.5` size bonus, `FirstEggHatchSeconds = 5` (Economy:67-70).
* Second-egg tutorial (step Id Steal2 or Place2 when delivering, PenService:523-526): hatch time raised to at least `TutorialEggMinHatchSeconds = 40` (Economy:78).
* Newbie protection (600 s or until first hatch; PvPService:63,197) is granted independent of the tutorial; step 10 only describes it.
* "Tutorial complete!" toast (TS:58) whenever `setStep(DONE)` runs (also fires from `inferStart` fresh-start path, see 5).
* Muting while `Tutorial.InProgress` (step < 17): quest-complete toasts (RetentionService:211, :236; QuestsController:818) and "you can afford" toasts (UpgradeHintController:83). Quest progress still counts.

## 5. When onboarding is saved as done; legacy markers; inferStart

* Done = `TutorialStep == 17` in `profile.Data`. No separate "done" flag. Reached by `setStep(DONE)` from step 16 (trail owned), or silently by `inferStart` (below), or via the first branch for fresh saves with `WheelTier >= 1`.
* `inferStart(player, data)` (TS:72-98) runs on `DataService.Loaded` (TS:157) and on `DataService.Wiped` (TS:158-161, admin wipe starts the tutorial over), and for already-present players at Start (TS:162-167):
  * TS:74-77 `data.TutorialStep == Tutorial.LegacyDone (8) and ((data.Rebirths or 0) > 0 or #data.Pets >= 6)` -> `Set DONE` silently ("veterans who finished before steps 8-10 existed").
  * TS:78-81 `data.TutorialStep == Tutorial.LegacyDone2 (11) and data.Stats.EggsDelivered >= 2` -> `Set DONE` silently.
  * TS:82-85 `data.TutorialStep == Tutorial.LegacyDone3 (16) and ownsTrail(data)` -> `Set DONE` silently.
  * TS:86-88 `if data.TutorialStep ~= 0 then return`.
  * TS:89-97 for step 0: `STEAL`; `WheelTier >= 1` -> `DONE` (via `setStep`, so the toast fires and `StepChanged` fires); `#Pets > 0` -> `BUY_WHEEL`; `#Eggs > 0` -> `HATCH`.
* Caveat for the critic: the legacy values 8, 11 and 16 are now ALSO live step indices (Index, Steal2, Trail). The three silent-skip rules therefore also hit any live player who rejoins while sitting on those steps if the extra condition holds. Example: a player on step 11 (Steal2) with `EggsDelivered >= 2` (possible because steps 8-10 do not block play) who rejoins is marked Done and skips steps 11-16, including the Trail purchase step. Step 8 with 6+ pets or rebirths likewise skips. Only evaluated on load, not mid-session.
* `inferStart` only advances forward-from-0 or silently completes; there is no rule that downgrades a stale mid-state on load (e.g. step 2-4 with no carried egg is repaired by the 1 Hz loop instead, TS:199).
* Funnel flag `data.Funnel = { Decided, New, Step }` (DataService:104, not replicated) is separate from tutorial progress (see 7).

## 6. Client rendering (TutorialController)

* Binds `Hud.Tutorial` (CanvasGroup with `Title`, `Body`, `Step` labels, a UIScale) (TC:399-406). All of these instances exist in StarterGui (verified): `Hud.Tutorial`, `Hud.Spotlight`, `Hud.FirstWalk.Distance`, `Menus.Index.TutorialTip`, `ReplicatedStorage.TutorialArrow`.
* Step source: `DataController.OnChanged("TutorialStep")` (TC:755-758) and the initial `DataController.Get` (TC:759-762; if nil it waits for the snapshot "avoids flashing step 1 at veterans"). Step 0 renders as step 1 (`effectiveStep`, TC:87-89).
* `showStep` (TC:421-463): sets `Title`/`Body` from `viewOf`, `Step` label `` `{step}/{#Tutorial.Steps}` `` (e.g. "5/16"), fades the card in with a 0.85 -> 1 scale pop and plays Notify sound (not on first show). When `Tutorial.Steps[step]` is nil (step 17 / Done) the card fades out and hides (after the reveal if one is on).
* `viewOf` (TC:208-222): uses the step's `Pre` view (title/text/target) while `preActive` (TC:187-205: for `Until = "PetPlaced"`, true when the player has pets and none is Placed); for `SaveFor` steps appends ` ($money / $price)` while `Money < SaveFor`.
* Beam: ReplicatedStorage `TutorialArrow` cloned to Workspace as `TutorialGuide` (TC:370-397); beam enabled only when a target resolves (TC:693-696); positioned feet-to-target each RenderStepped (TC:735-741). Target kinds in `resolveTarget` (TC:106-181): NestEgg (nearest streamed Meadow egg part, else Meadow `NestCenter`), LockLine/ZoneEdge, Plot (floor centre), PlacedEgg/HatchEgg (incubating vs ready egg in `IncubatingEggs`; HatchEgg points at the plot floor while `CarryingEgg` is set), Wheel (`WheelPad`), WheelBoard (plot `Boards.TreadmillBoard` pivot, else wheel pad), Pen (`PenCenter`), TrailStand (tier-1 pedestal `PromptPart`, else world attr `TutorialTrailSpot`).
* Spotlight (steps with `Highlight`: 8 Index, 9 Daily): `HudController.Highlight(name, true)` pulses the side button (TC:408-419) and `setSpotlight(HudController.GetSideButton(name))` dims the screen with four gradient frames and a button-sized hole (TC:226-368); it fades out while any modal is open, during a reveal, or if the button is hidden/off-screen (TC:342-345). No other step uses it.
* Ack sender (TC:466-488): for current step >= 8 with an `Ack`: `OpenModal:<n>` ack as soon as that modal is open (unused by the config); `CloseModal:<n>`: marks opened while the modal is open, acks once it is closed again or after `AckSeconds` (default 120); `Timer`: after `AckSeconds` (default 8). Sends `RequestSaveSettings({ TutorialAck = currentStep })` once per step (`ackSent`). Called from the 0.2 s `update()` Heartbeat (TC:769-776).
* Index tip (TC:579-611): while step is Index and the Index modal is open, shows `Menus.Index.TutorialTip` pointing at `ClaimAll` ("Claim your reward!") or "All set! Close the Index to continue".
* Step 1 distance pill (TC:614-650): `Hud.FirstWalk.Distance` shows "Meadow nest: N studs" (rounded to 5, min 5) updated at ~4 Hz, or within 15 studs "Hold E on an egg!" / "Hold the button on an egg!" (touch).
* First hatch "Mama!" bubble + confetti (TC:490-576, trigger TC:763-767): on `PetHatched` when `1 <= currentStep <= 5` (HATCH_STEP = 5). Also fires for a Fuse result while the step is still <= 5.
* Reveal gating: while `HudController.IsRevealing()`, step changes are held and only the latest is applied after the reveal (TC:700-719).
* `HudController` stacks the card top-centre above event banners, the hint pill and toasts (HudController:661-681).
* `FirstWalkController` separately renders pickups/chevrons/whoosh only while `TutorialStep == 1` (FirstWalkController:427).

## 7. OnboardingService funnel (analytics)

* Purpose: `AnalyticsService:LogOnboardingFunnelStepEvent` for genuinely new players only. `onLoaded` (OB:57-66): `funnel.Decided` set once; `funnel.New = (data.TutorialStep or 0) <= 1 and data.Stats.EggsDelivered == 0 and #data.Pets == 0 and #data.Eggs == 0` (OB:63), then `reach(player, 1)`. Persisted in `data.Funnel` (DataService TEMPLATE :104); steps logged at most once, in order, `funnel.Step` advanced even if the call failed (OB:47-54, pcall'd).
* Because TutorialService starts earlier (Main ORDER), `inferStart` has already set `TutorialStep` to 1 for a brand-new player before `onLoaded`; that still satisfies `<= 1`.
* Walk steps 1-4 use a 2 Hz loop (OB:83-113): 2 LeftSpawn (>= 25 studs from `LobbySpawn`), 3 EnteredMeadow (`WorldIndex.InCorridor`), 4 ReachedNest (within 22 studs of `meadow.NestCenter`).
* Tutorial steps map tutorial step t (2..17) to funnel index t + 3 via `TutorialService.StepChanged` (`reach(player, WALK_STEPS + step - 1)`, OB:76-80). Step 1 (Steal) has no event of its own (it coincides with the walk).

| Funnel | Name | Funnel | Name |
|---|---|---|---|
| 1 | Joined | 11 | Tut8_Index |
| 2 | LeftSpawn | 12 | Tut9_Daily |
| 3 | EnteredMeadow | 13 | Tut10_Protection |
| 4 | ReachedNest | 14 | Tut11_Steal2 |
| 5 | Tut2_Escape | 15 | Tut12_Place2 |
| 6 | Tut3_Home | 16 | Tut13_PickUp |
| 7 | Tut4_Place | 17 | Tut14_WheelRoll |
| 8 | Tut5_Hatch | 18 | Tut15_Hatch2 |
| 9 | Tut6_BuyWheel | 19 | Tut16_Trail |
| 10 | Tut7_Wheel | 20 | Tut17_Done |

* Notes: `OB` does not listen to `DataService.Wiped`; after an admin wipe `data.Funnel` is back to `{Decided=false, New=false, Step=0}` and nothing re-decides it, so wiped accounts log nothing further. `OnboardingService._steps` exposes the table for tests (OB:116).

## 8. Hints (Config.Tutorial.Hints, Cfg:85-92) and HintController

Each hint is shown once per player; seen flags are saved through `RequestSaveSettings({ HintSeen = key })` into `data.Settings.Hints[key]` (SS:54-65 validates the key against `Tutorial.Hints`, key length <= 24, rate key "SettingsMeta" 0.4 s). Shown in a runtime-built `Hud.HintPill` for 5.5 s, one at a time, >= 8 s apart (`SHOW_SECONDS`, `GAP_SECONDS`, HC:34-35, :104-128). Never starts during a hatch reveal (HC:109-111, not marked seen). Check loop every 0.5 s under pcall (HC:226-236). Order of checks in `check()` (HC:153-224) is also the priority order.

| Key | Text | Trigger (HintController) |
|---|---|---|
| HeavyEgg | Heavy eggs slow you down - bigger egg, slower roll! | carrying (`CarryingEgg` attribute) and `Curves.DragMultiplier(CarrySize) <= 0.85` (HC:166-171) |
| GrowLine | Eggs keep growing as you roll them - cross the gold line to be safe. | carrying, `CarrySafe ~= true`; if `TutorialStep == 2` it is marked seen silently (tutorial already says it, HC:174-175); else shown once carrying > 2 s (HC:173-179) |
| Night | Night resets the field - eggs still out at dark roll back to their nests! | `CyclePhase == "Night"` or Day with < 25 s left (`NIGHT_WARN`) (HC:181-187) |
| Trap | Traps stop guards too - drop one behind you in a chase. | has a Tool with `PvPTool` attribute and `Tools.Trap > 0` (HC:190-195). Default data gives `Tools = {Bat=1, Trap=1}` and PvPService issues the tools at load, so this is eligible almost immediately for every new player |
| Fuse | Fuse needs 3 of the same pet - you're one away! | owns 2 of the same PetId (HC:197-208) |
| Protected | Protected players (shield) can't be hit or trapped. | has a PvP tool, is NOT protected itself, and a `Protected == true` player is within 20 studs (HC:210-223) |

`HintController.Show(key)` is exported (HC:131) but nothing calls it from other controllers in this snapshot (hints are only triggered by `check()`).

Interaction observations: the Trap hint can appear on a brand-new player's screen during the first steps (competing with the tutorial card, gap rule only spaces hints from each other); the Protected hint cannot reach a brand-new (protected) player. `HintSeen` and `TutorialAck` share the 0.4 s "SettingsMeta" limiter, so a TutorialAck sent within 0.4 s of a HintSeen is dropped (client never retries; see remotes.md flag 4).
