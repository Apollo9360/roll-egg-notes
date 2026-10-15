# Services and controllers inventory (read-only snapshot, Edit datamodel, studio 1973f6fb)

Conventions: paths shortened. `SSS` = `ServerScriptService.Server`, `SPS` = `StarterPlayer.StarterPlayerScripts`. Line = total lines in `.Source`. "Handles" = `OnServerEvent`/`OnServerInvoke`/`OnClientEvent` registered in that file (file:line). "Fires" = `FireClient`/`FireAllClients`/`FireServer`. All remotes go through `ReplicatedStorage.Shared.Net`; the `ReplicatedStorage.Remotes` folder does not exist in Edit mode (Net creates it at server runtime, destroying any existing one, `Net.lua:74-86`). Nothing bypasses Net (grep for "Remotes" finds only Net.lua:61 and a CustomPrompts comment).

## 1. Server entry and boot order

* `SSS.Main` (Script, 69 lines, enabled): requires every ModuleScript in `SSS.Services` under pcall, sorts with an ORDER list first (`DataService, WorldService, PlotService, SpeedService, EggService, GuardService, PenService, EconomyService, TutorialService`), the rest alphabetical. Runs every `Init()` (pcall, no yield), then every `Start()` in its own `task.spawn`+pcall. A service that fails to require is only `warn`ed and silently skipped (Main:40-46). Note: `OnboardingService.Start` therefore runs after `TutorialService.Start`, so on `DataService.Loaded` `TutorialService.inferStart` runs before `OnboardingService.onLoaded`.
* `SSS.RateLimit` (ModuleScript, 31 lines): `RateLimit.Allow(player, action, cooldown)`, per-(player, action key) `os.clock()` gate, a key is only stamped when the call is allowed, cleared on PlayerRemoving. Shared keys: any two code paths using the same key share one cooldown (example: BossService "BossHit" is used by both the ClickDetector and `RequestBossHit`).
* `SSS.Announcer` (ModuleScript, 43 lines): server-wide/personal toast helper over `Notify`. `AllAfter`, `ToPlayer(player, text, kind, afterReveal)`; constants `HATCH_FX_SECONDS`, `REVEAL_SECONDS`, `CARD_SECONDS`. Fires `Notify` via `FireAllClients` (Announcer:24,30) and `FireClient` (:39, tag "AfterReveal" when `afterReveal`). Handles no remotes.
* `ServerScriptService.Packages`: `SSS.Packages.ProfileStore` (2245 lines, third-party session-locked DataStore wrapper; not reviewed).

## 2. Server services (`SSS.Services.*`)

| Service | Lines | What it does | Handles (OnServerEvent/Invoke) | Fires |
|---|---|---|---|---|
| DataService | 321 | ProfileStore persistence (`PlayerData_v1`, key `Player_<UserId>`), TEMPLATE (:80-105), REPLICATED key set (:108-113), `Get/Set/Increment/Spend/MarkChanged/Wipe/Save`, leaderstats. Kicks if profile fails to load or session ends elsewhere. `Funnel` is not in REPLICATED. | `GetSnapshot` OnServerInvoke :299 | `DataChanged` :155 (FireClient) |
| WorldService | 103 | Day/night cycle on `Workspace.World` (Day 290 s, Night 10 s), mirrors `CyclePhase`/`CycleEndsAt` attributes, `ExtendDay`. | none | `CycleChanged` :32, :55 (FireAllClients; no client listens) |
| PlotService | 339 | One plot per player, spawns/teleports characters (`TeleportHome`), fence skins, `GetIndex/IsInOwnPlot/PlotAt`. | none | none |
| SpeedService | 257 | Owns Speed stat and Humanoid.WalkSpeed (`WalkSpeedFromSpeed * drag * slow`), hamster-wheel models and the on-wheel Speed gain, `AddSpeed(player, amount, source?)` (:83). | none | none |
| EggService | 1083 | World eggs: nest/carried/loose eggs, carry growth, lock-line safety, placement, night reset, `Grabbed/ReachedSafety/EnteredPlot/Delivered` signals. Nest grab is a ProximityPrompt (`prompt.Triggered` :208). | `RequestDropEgg` :1003, `RequestPlaceEgg` :1008 | `Notify` (:591,617,789,796,813,832,1036) |
| GuardService | 617 | Kinematic guard AI per biome (Sleep, Waking, Chase, Catch/GiveUp, Return), personalities, trap root. | none | `GuardEvent` :394 (FireAllClients), :424 (FireClient "Caught"), `Notify` :411 |
| PenService | 972 | Incubation, hatching, pet pen, pet hotbar tools, acceptDelivery (starter egg rules :492-551), hatch (:553), pick-up/Roll prompt (:643), Skip prompt (:674), sell. Prompts: `prompt.Triggered` :190 (Hatch), :210 (Roll), :229 (Skip). | `RequestHatch` :861, `RequestPlacePet` :866, `RequestPlacePetAt` :871, `RequestSellPet` :879 | `PetHatched` :632, `Notify` (:549,576,653,709,748,752,756,785,814) |
| EconomyService | 111 | Passive income each 1 s, offline earnings (3 s after load, 50%, max 8 h), cash purchases of Wheel/Pen tiers. | `RequestBuy` :84 | `Notify` (:40,45,57,66,79) |
| TutorialService | 232 | First-session tutorial state machine (see tutorial.md). `StepChanged` signal, `Ack`. | none directly (called from SettingsService via `TutorialService.Ack`) | `Notify` :58 ("Tutorial complete!") |
| PlazaService | 299 | Lobby "biggest egg today" board and 3D holder statue. (`HolderRig` child 123 lines: R15 trophy-pose model surgery.) | none | none |
| PlotBoardService | 54 | Mirrors Pen/Wheel levels onto plot attributes for the porch boards. | none | none |
| AdminService | 165 | Admin gate and tools: wipe, live boosts, event triggers. Admin = `ADMIN_USER_IDS` {1570336050}, place owner, or any Studio session (`RunService:IsStudio()` :46). Sets `IsAdmin` attribute. | `AdminBoost` :115, `AdminEvent` :131, `AdminWipe` :139 | `Notify` (:73,78,87,89,99,104,111) |
| BoostService | 49 | Writes `Boost_<Kind>`/`BoostEnds_<Kind>` Workspace attributes that `Shared.Multipliers` reads. | none | none |
| RetentionService | 473 | 7-day daily calendar, 3 daily quests (+reroll), codes, quest signal hooks. | `RequestClaimDaily` :375, `RequestClaimQuest` :380, `RequestRerollQuest` :385, `RequestRedeemCode` :390 | `QuestProgress` :209,234, `Notify` (:154,164,212,237,260,266,283,293,299,312,316,323,329) |
| FuseService | 123 | Fuse machine: 3 same pets to 1 pet of next rarity (keeps largest size, best mutation/tier). | `RequestFuse` :102 | `PetHatched` :97, `Notify` :60,68 |
| TrailService | 267 | Cash/Robux trails, equip, `TrailMult` attribute, visual. Stand prompts via `ProximityPromptService.PromptTriggered` :231. | `RequestBuyTrail` :232, `RequestEquipTrail` :237 | `Notify` (:152,157,161,168,184) |
| PvPService | 1021 | Bat and Trap tools, bear traps, newbie protection (600 s), arena hook, hotbar tool management. | `RequestBat` :935, `RequestPlaceTrap` :943 | `PvPEvent` (:403,414,425,426,623), `Notify` (:266,420,574,578,582,590,596,613,624,626,635) |
| RebirthService | 181 | Rebirth: reset Money/Speed/Wheel/Pen, keep pets/index/trails, +50%/rebirth. | `RequestRebirth` :154 | `Notify` :113,148,149 (149 is FireAllClients) |
| SocialService | 87 | Friend boost (+10%/friend, cap +50%), `FriendBoost` attribute. | none | `Notify` :69 |
| LeaderboardService | 297 | OrderedDataStore leaderboards (`LB_BiggestEgg`, `LB_TotalEarned`, `LB_Rebirths`), lobby boards, Studio fallback. | none | none |
| EventService | 289 | Live-ops scheduler (Capture, Boss, Night Run), rare Rainbow-egg spawns, `Trigger(event)` (incl. "HugeEgg" :215). | none | `EventState` (:97,105,149,168,281), `Notify` (:98,106,148,167,179) |
| MonetizationService | 662 | Robux catalogue grants, receipts (`ProcessReceipt` :575), passes, timers, Studio fake grants (`FakeInStudio = RunService:IsStudio()` :70). | `RequestPromptProduct` :579 | `PurchaseGranted` :441,554,616, `Notify` (:440,533,541,546,553,615,626,648,653) |
| SettingsService | 116 | Validates/saves `data.Settings`, `HintSeen`, `TutorialAck`. | `RequestSaveSettings` :112 | none |
| CaptureService | 518 | Capture-the-Egg lobby event (arena, hold-timer, rewards). Grab prompt `prompt.Triggered` :221. | none | `EventState` :187,490, `Notify` (:301,320,324,346,396,420,429,483) |
| BossService | 540 | Egg Golem co-op boss. ClickDetector (`click.MouseClick` :443) or remote hit, bat swing hook (`PvPService.Swung` :519). | `RequestBossHit` :513 | `EventState` :261,529, `PvPEvent` :343, `Notify` (:372,417,419,450) |
| IndexService | 445 | Pet Index discovery, per-pet claims (capped), zone-completion bat skins, equip skin. | `RequestClaimIndex` :407, `RequestClaimIndexPet` :412, `RequestClaimIndexAll` :417, `RequestEquipBatSkin` :422 | none (uses `Announcer.ToPlayer`) |
| DevService | 79 | Studio-only QA `BindableFunction` "DevHook" in ServerStorage; `Init` returns early outside Studio (:62). | BindableFunction OnInvoke :67 (Studio only) | none |
| OnboardingService | 119 | Funnel analytics via `AnalyticsService:LogOnboardingFunnelStepEvent` for new players (see tutorial.md). | none | none |
| HugeEggService | 248 | Every 3rd reset spawns a guaranteed Huge-tier egg; `SpawnNow()` (admin hook via EventService). Header comment says "not wired into AdminService.EVENTS" but AdminService.lua:43 does list `HugeEgg` and EventService.lua:215 handles it (stale comment). | none | none (Announcer) |
| FirstWalkService | 131 | Speed pickups on the first walk (+2 Speed each, 13 pickups in `Workspace.World.Lobby.FirstWalk.Pickups`). Pure `core.Handle` is testable. | `RequestWalkPickup` :122 | none |
| BiggestEggService | 158 | "Biggest egg today" tracker over OrderedDataStore `LB_BiggestEggDay_<yyyymmdd>`. Child `Core` (230 lines): pure logic. | none | none |

Remotes with only a server definition and no service handler in this table: `RequestGrabEgg`, `RequestWheel` (legacy, no handler; grabs use ProximityPrompts, wheel is detected by SpeedService).

## 3. Client entry, shared UI and Fx

| Module | Lines | Purpose |
|---|---|---|
| `SPS.Client.Main` (LocalScript, enabled) | 55 | Client entry. Requires every `Controllers.*` under pcall, ORDER `DataController, SoundController, HudController, PlacementController, TutorialController` first then alphabetical, all `Init` then all `Start` (task.spawn+pcall). |
| `SPS.Client.UI` | 475 | Instance builder, theme, button binding (hover/press juice). |
| `SPS.Client.UIFx` | 541 | Self-restoring tween effects for GuiObjects (shared baseline rule). |
| `SPS.Client.UIFxParticles` | 621 | GuiObject-based particles (confetti, coins, rings). Cap 250 burst / 60 ambient. |
| `SPS.Client.RevealGate` | 245 | Pure logic gate that holds toasts while a hatch reveal is on screen, flushes after (hard TIMEOUT). Owned by HudController. |
| `SPS.Client.Fx.Spring` | 414 | Analytic damped springs for any property. |
| `SPS.Client.Fx.TextFx` | 1139 | Per-letter text effects. |
| `SPS.Client.Fx.ModalFx` | 1881 | Modal open/close "juice" sequencer. None of the Fx/UI modules touch remotes. |

## 4. Client controllers (`SPS.Client.Controllers.*`)

Column "Handles" = OnClientEvent; "Fires" = FireServer. Controllers not listed with remotes read replicated data through DataController and Workspace/Player attributes only.

| Controller | Lines | What it does | Handles | Fires |
|---|---|---|---|---|
| DataController | 52 | Read-only local mirror of replicated data; initial `GetSnapshot` invoke. | `DataChanged` :32 | invokes `GetSnapshot` :39 |
| SoundController | 201 | Plays named sounds from `Shared.Assets.Sounds`, honours Music/Sfx settings. | none | none |
| HudController | 1336 | Main HUD and menus (stats, egg timer, carry card, side buttons, Shop modal, Eggs/Pets panels, toasts, hatch reveal, `Highlight`/`GetSideButton` used by tutorial). Owns the RevealGate. | `Notify` :1252, `PetHatched` :1258 | `RequestHatch` :379, `RequestPlacePet` :417 and :910 ("BEST"), `RequestSellPet` :423, `RequestBuy` :453 (Wheel), :465 (Pen), `RequestDropEgg` :897, :1302 |
| CarryController | 563 | Renders the rolled egg for every carrier (visual only). | none | none |
| GuardFxController | 460 | Guard presentation: Zzz, alert, chase music, limb swing, knockback/shake on catch. | `GuardEvent` :446 | none |
| BaseFxController | 406 | Egg countdown billboards, pet wandering tweens, +$/s pops, hide-other-pets. | none | none |
| PlacementController | 230 | Ghost egg + click/tap to place on own plot. | none | `RequestPlaceEgg` :128 (`placeRemote:FireServer(spot)`, remote bound :184) |
| TutorialController | 781 | Tutorial card, guide beam, HUD spotlight, ack sender, Mama bubble (see tutorial.md). | `PetHatched` :763 | `RequestSaveSettings {TutorialAck}` :486 |
| PlotBoardController | 193 | Porch upgrade boards (pen / wheel) from plot attributes. | none | `RequestBuy` :128 |
| EggFxController | 51 | Rainbow-variant hue cycling. | none | none |
| AdminController | 199 | Binds StarterGui.Admin; toggle shows only if `IsAdmin` attribute. | none | `AdminWipe` :106, `AdminBoost` :152,159, `AdminEvent` :191 |
| PlotEggController | 482 | Placed-egg presentation: luck tag, drop-in, hatch animation; opens reveal gate. | none | none |
| PetToolController | 155 | Hotbar pet ghost over pen; click places. | none | `RequestPlacePetAt` :71 |
| SpeedFxController | 194 | Speed counter and flying "+N" popups. | none | none |
| WheelFxController | 297 | Hamster wheel drop-in/spin/upgrade fx. | none | none |
| ShopController | 1111 | Robux shop modal; Buy confirms then fires prompt request. | `PurchaseGranted` :1077 | `RequestPromptProduct` :288 |
| IndexController | 1320 | Index menu: pet grid, claims, bat skins. | none | `RequestClaimIndexPet` :914, `RequestClaimIndex` :992, `RequestEquipBatSkin` :1072, `RequestClaimIndexAll` :1300 |
| WheelRunController | 107 | Faces run direction and plays walk anim in the wheel (attribute `OnWheel`). | none | none |
| CombatController | 685 | Bat/Trap tool activation, protection pill, knockback and hit feedback. | `PvPEvent` :614 | `RequestBat` :271, `RequestPlaceTrap` :370 |
| RebirthController | 705 | Rebirth menu with confirm step. | none | `RequestRebirth` :675 |
| DailyController | 877 | Daily calendar modal + codes row. | none | `RequestRedeemCode` :729 (substring to 32 chars), `RequestClaimDaily` :850 |
| QuestsController | 853 | Quests modal. | `QuestProgress` :848 | `RequestClaimQuest` :679, `RequestRerollQuest` :688 |
| TrailController | 284 | Trails menu. | none | `RequestEquipTrail` :107, `RequestBuyTrail` :111 |
| UpgradeHintController | 159 | One-time "you can afford X" toasts (muted while tutorial in progress :83) + friend boost pill. | none | none |
| FuseController | 501 | Fuse menu. | none | `RequestFuse` :433 |
| PetIdleController | 602 | Cosmetic idle tricks for pets (local clones). | none | none |
| BoostStripController | 267 | HUD boost pills (server boosts, Luck token, Night Run). | none | none |
| EventController | 985 | Event banners, Night look lighting, guides to arena/boss, rainbow egg highlight. | `EventState` :965 | none |
| TrailStandController | 220 | Lobby trail stand dressing. | none | none |
| SettingsController | 926 | Settings menu (sliders, toggles, code box). | `Notify` :672 (watches toast kind for code redemption) | `RequestSaveSettings` :120, `RequestRedeemCode` :717 |
| HintController | 239 | One-line contextual hints (see tutorial.md). | none | `RequestSaveSettings {HintSeen}` :62 |
| FeelController | 151 | Catch "slow-mo" beat (FOV punch, vignette). | `GuardEvent` :143 | none |
| DevUIController | 70 | Studio-only QA BindableFunction `LocalPlayer.DevUI`. | none | none |
| HugeEggController | 75 | Lobby arch sign text from Workspace `HugeEggAt/HugeEggLive`. | none | none |
| FirstWalkController | 452 | First-walk pickups, chevrons, whoosh (TutorialStep == 1 only, :427). | none | `RequestWalkPickup` :123 |

Server-to-client events with NO client listener: `CycleChanged` (clients read `CyclePhase`/`CycleEndsAt` attributes instead), `CarryChanged` (never fired or listened to anywhere).

## 5. Other scripts that run (all BaseScripts in the place outside Server/Client trees)

| Path | Class | Lines | Enabled | Notes |
|---|---|---|---|---|
| `ServerScriptService.SoftShutdown` | Script | 86 | enabled | Third-party SoftShutdown 1.2 (teleports players to a reserved server on `game.OnClose`; reserved server shows a Message and sends everyone back). Child `UpdateUI` ScreenGui with LocalScript `_do` (22 lines, enabled). |
| `StarterPlayer.StarterPlayerScripts.CustomPrompts` | LocalScript | 501 | enabled | Third-party "Proximity Prompt Customizer" (Creator Store 8370062439, header says audited: no remotes/HTTP/loadstring). Renders only prompts with `Style = Custom`. Children: BillboardGui templates Default/Steal/Hatch/Roll/Skip; `README` LocalScript (62 lines) DISABLED. |
| `StarterPlayer.StarterPlayerScripts.TEST` | LocalScript | 810 | DISABLED | Old test script with BillboardGui children (Green, Sell, Steal, RoundCarrySteal, OGSteak, Hatch). Inert but ships in the place. |
| `Workspace.TopbarPlus.READ_ME` | Script | 36 | DISABLED | RunContext Client. TopbarPlus folder otherwise has no active scripts here. |
| `ServerStorage.YetiIdleAnimation` | Script | 86 | enabled flag but in ServerStorage, so it never runs | |

Also present but inert (scripts live under ServerStorage, which never executes): `ServerStorage.REFERENCE_PLEASEUSE.*` (many LocalScripts/Scripts, reference only), `ServerStorage.Quarantine.*` (disabled), `ServerStorage.Removed_LobbyDecor.*` (disabled), `ServerStorage.Backup_PreBlocky_2026-10-12.World.*` (enabled flags but in ServerStorage, inert). Not read or copied per the rules.

No Scripts/LocalScripts exist in: `ReplicatedFirst` (empty), `StarterPlayer.StarterCharacterScripts` (empty), `StarterGui` (4 ScreenGuis: Hud, Menus, Admin, Events, all Enabled=true, containing no scripts; all GUI logic is in the controllers), `StarterPack`, `ReplicatedStorage`, `Workspace` (apart from the disabled TopbarPlus READ_ME).
