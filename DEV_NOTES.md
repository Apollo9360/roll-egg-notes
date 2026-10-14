# Roll and Steal an Egg! — Dev Notes

Running log of every change to the Studio place, plus the raw Steal an Egg research at the
bottom to guide design decisions. Newest entries first. Add to this file with every change.

- Place: "Roll and Steal an Egg!" — place 122907972658396, universe 10770455512
- Workflow: Studio-only (Studio MCP). Builds must be real instances (Workspace / StarterGui), not script-only.
- Companion file: [KEY_VISUALS.md](KEY_VISUALS.md) (key visuals, dream pets, image prompts)

## Standing rules

- `ServerStorage.REFERENCE_PARKED` (the analyzed Steal an Egg rip) is **consult-only**. Never copy its
  code or assets into the game. Delete it before publishing.
- Toolbox assets: insert into `ServerStorage.Quarantine` first, audit for scripts / Humanoids / Values,
  then copy cleaned versions out (`ServerStorage.DecorLibrary`). Avoid keyword-spam uploads from
  throwaway accounts (common backdoor source).
- Decoration uses Toolbox models, not part-built props. Gameplay markers (zone edges, beacons, nests,
  fences, wheel pads) may stay parts.
- UI lives in StarterGui (`Hud`, `Menus`); controllers only bind and clone `Templates`.
- Reference UI image IDs are used because the user stated they are open-use; all live in
  `Shared.Config.Icons` (images in StarterGui carry an `Icon` attribute and are re-applied at runtime).
- Don't `require` game modules from `execute_luau` to read live state (fresh instances). Edit-mode
  `require` caches modules: clone the module (or the whole DevTools folder) to rebuild with new code.
- `ServerScriptService.YetiIdleAnimation` is not ours (waits on a missing `Workspace.Yeti`). Leave it.
- Studio `screen_capture` does not show ProximityPrompt UI; they do render for players.

## Changelog

### 2026-10-13 — Skip timer prompts (Instant Hatch on one egg)
- **New third prompt on every still-incubating placed egg (PenService.refreshIncubators):** `SkipPrompt`.
  - Theme "Skip", key R / gamepad X, hold `Economy.Prompts.SkipHold` = 0.25 s.
  - OwnerOnly. `UIOffset (0,-78)` puts it under the Roll prompt.
  - Text: "Skip  R$ <PriceHint>" / "Hatch now". The price is the Instant Hatch tier for THAT egg's time left (`Products.HatchItemFor`). The 1 Hz loop refreshes it, so it steps down as the timer runs: T4 299 → T3 149 → T2 49 → T1 9.
  - Hidden once the egg is ready; the Hatch prompt takes over.
- **Server flow:** Triggered → PenService `skip()` (rate limit 1 s; egg exists, not carried or hatching, time left > 0) → `PenService.OnSkip` (set in MonetizationService:Init) → `promptProduct(player, "HatchT1", remaining)`.
  - `promptProduct` gained an optional `hatchRemaining`: the tier is for that egg, not the longest one (the Shop path is unchanged).
  - The existing receipt grant finishes every egg with ≤ the tier's MaxSeconds. No new products; uses the existing HatchT1–T4 ids.
- **New CustomPrompts template `Skip`:** a clone of Roll recoloured to Robux purple (183,0,255): key cap, outline, ring; dark purple pill.
- **Not verified on screen:** prompt templates don't render in Edit mode. Check the Roll/Skip stacking in a playtest; if the offset goes the wrong way, flip the sign of `SKIP_UI_OFFSET`.

### 2026-10-13 — Spotlight snapping to centre (root cause)
- **Cause:** HudController's layout manager (`manage(obj)` over every Hud child) stored Hud.Spotlight's StarterGui Position {0.5,0.5} as a slot. `commitLayout` rewrote it whenever the layout re-ran (e.g. during the speed fly-in), fighting TutorialController. Confirmed live on the client: Position was reset to {0.5,0.5} twice per frame.
- **Fix (made by the user):** HudController removes Spotlight from `slotPos`, like RunRed and FxLayer.
- **Rule for future Hud children that code positions itself:** exclude them from `slotPos` there too.

### 2026-10-13 — Spotlight fix (Scale, no jumping) + time to claim Index rewards
- **TutorialController spotlight rewrite:**
  - Never writes Size: the asset keeps its {2,0},{2,0} Scale.
  - Position is now `UDim2.fromScale` of the button centre relative to the Hud's own origin and size. Hud uses DeviceSafeInsets, so the old pixel offsets were shifted on phones.
  - The hole size is computed from `Spotlight.AbsoluteSize`.
  - It fades out, keeping its place, while:
    - any menu is open (new `HudController.GetOpenModal()`);
    - a hatch reveal runs (reveal mode slides the tabs off-screen);
    - the button is hidden, faded, zero-size or off-screen.
  - It comes back when the button does. It irises in only once per target, never on every hide and show.
- **Index / Daily steps:** Ack changed from `OpenModal:` to the new `CloseModal:`. The step advances when the player closes the menu, so they have time to claim inside it. AckSeconds = 120 is the fallback if the menu is never opened (the server auto-advances at 122 s).
  - The Index text now says to claim the reward and then close the menu.
- **New instance `StarterGui.Menus.Index.TutorialTip`** (Arrow + Label; Rubik Bold, yellow, black stroke):
  - Shown only on the Index step while the Index menu is open.
  - "Claim your reward!" with a hopping ▲ under Claim all (attribute ClaimPosition).
  - "All set! Close the Index to continue" once nothing is claimable (attribute ClosePosition).

### 2026-10-13 — Tutorial fixes, spotlight, trail step, real night
- **Step 3 text (Config.Tutorial Home):** "Roll your egg onto your plot. It keeps growing with every step you take!" No wheel mention.
  - HudController.refreshCarry carry hints check `WheelTier >= 1`. Without a wheel they say "Click your plot to place it" / "Safe egg: keep walking to grow it, then roll it onto your plot".
- **Step 4 (BuyWheel):**
  - Target is now `WheelBoard` (the plot's Boards/TreadmillBoard, falling back to WheelPad).
  - New step field `Pre = { Until = "PetPlaced", … }`: until a pet is placed, the client shows "Place your pet" and points at the pen (`Pen` → plot.PenCenter).
  - Swapping between the Pre view and the main step pops the card and plays a sound.
  - New `SaveFor` field: the card shows " ($X / $Y)" while the player can't afford the step yet.
- **Toasts muted during the tutorial:**
  - `Tutorial.InProgress(step)` gates the RetentionService "Quest complete" notifies.
  - It also gates the QuestsController claim toast and UpgradeHintController's "you can afford" hints.
  - Hints aren't marked seen while muted, so they show after the tutorial.
- **Spotlight (TutorialController):**
  - Highlight steps (Index, Daily) now use `StarterGui.Hud.Spotlight` instead of only the outline.
  - The frame is sized to 2× the screen and centred on the button.
  - Each side's UIGradient is rewritten so the clear hole is about 1.3× the button + 24 px, with a slight pulse, irising in from 0.8× the screen. It fades in and out over 0.35 s.
  - Edge darkness, rim darkness and soft width are read from the asset's own keypoints at runtime, so retuning the asset carries over.
  - ZIndex: Spotlight = 30, Hud.Tutorial card = 40, so the card stays on top.
- **Step 16 "Get a trail" (appended; Done = 17, LegacyDone3 = 16):**
  - Hatch2 now advances to Trail instead of Done.
  - Completes when `Trails.Owned >= 1`. Old saves at 16 that already own a trail go straight to Done.
  - The beam targets the Tier 1 TrailStand pedestal's PromptPart, falling back to the new `workspace.World` attribute `TutorialTrailSpot`.
  - The card shows the money progress toward $5,000.
- **Cycle night is visibly night (EventController):** `refreshLook()` applies the Night Run lighting whenever Night Run is active **or** `Workspace.CyclePhase == "Night"`. A shared `lookOn` flag stops the two from fighting. The tween takes 3 s inside the 10 s night.

### 2026-10-13 — Pick placed eggs back up, wheel hatch timer, second-egg tutorial
- **Roll prompt (PenService.refreshIncubators):** every placed egg now has two prompts:
  - `HatchPrompt` (E), shown only when the egg is ready;
  - `RollPrompt` (F, gamepad Y, hold 0.4 s, new cyan theme "Roll"), shown only while it is still incubating.
  - The two never show at the same time.
  - Both carry `OwnerOnly = UserId`: CustomPrompts disables them locally for other players, so they don't show or take the key.
- **Picking up (PenService.pickUp → EggService.CarryIncubating):**
  - The egg is carried like any rolled egg: same attributes, secured (never hunted), no `Grabbed` signal (no quest, guard or tutorial credit).
  - It keeps its size and pet; it does not grow.
  - Its EggRecord stays in `data.Eggs` with `Carried = true` (new optional field) and its part is removed.
  - Ready eggs can't be picked up; hatch them instead.
- **Placing it again (EggService.placeEgg → Incubator.Replace):** moves the record to the new spot, which is validated by the shared `checkSpot`. It drops in from the sky again. This is not a new delivery: no `Delivered` signal and no stats.
- **Never lost:** any other end of the carry puts it back on its old spot (`Incubator.Release`): a drop (Q), a guard catch, a bat hit, death, night in the corridor, leaving, or an admin drop. `Carried` flags are also cleared when data loads.
- **Hatch timer while carried:** it keeps running at the normal rate.
  - Inside your own hamster wheel it runs `1 + WheelHatchBonus (2) + WheelHatchBonusPerTier (0.25) × (tier − 1)` times as fast: 3x at tier 1. That speed-up is added to the record's `Bonus`.
  - Clients get `CarryIncubating`, `CarryHatchLeft`, `CarryHatchRate` and `CarryHatchAt`, and count down locally. They are re-sent when the rate changes or the countdown drifts by more than 1.25 s.
- **Visible timer:**
  - CarryController: the carried egg's label swaps the clover and luck for an hourglass and an `m:ss` countdown. It is white normally; cyan, with a spinning hourglass, while the wheel speeds it up; green "READY!" with a pop and sound at 0.
  - HudController carry card: shows "Hatches in m:ss" and the speed penalty, with hints for the wheel, the speed-up and ready.
- **Tutorial (appended steps 11–15, Done = 16):**
  - The steps are Steal2 → Place2 → PickUp → WheelRoll → Hatch2.
  - They are state-based (`TutorialService.secondEgg`, 1 Hz). Being carried around out of order works, e.g. a second egg already incubating jumps to PickUp, and setting it down early goes back to PickUp.
  - The egg delivered during Steal2/Place2 hatches in at least `TutorialEggMinHatchSeconds = 40`, so there is a timer to watch shrink.
  - Hatch2 ends on `PenService.Hatched`.
  - Beam targets:
    - PlacedEgg now prefers an egg that is still incubating.
    - New HatchEgg prefers a ready egg, or points at the plot while you're still carrying.
- **Tutorial compatibility:**
  - Old saves sitting at 11 (the old Done) with 2+ delivered eggs skip to Done on load (`Tutorial.LegacyDone2`).
  - Ack (auto-advance and client confirm) is now limited to steps with an `Ack` field, so 11–15 never time out.
- **Files:**
  - Economy (Egg.WheelHatchBonus, WheelHatchBonusPerTier, TutorialEggMinHatchSeconds; Prompts.RollHold)
  - Tutorial (steps, LegacyDone2, IndexOf)
  - DataService (EggRecord.Carried)
  - PenService, EggService, TutorialService
  - CustomPrompts, plus the new `Roll` template next to Default/Steal/Hatch
  - CarryController, HudController, TutorialController
- Verified statically: every changed script compiles (loadstring). Not playtested.

### 2026-10-13 — Starter egg: guaranteed Epic Owl, no traits
- **Economy.Egg:** `FirstEggGuaranteedPet` changed from "Chick" to "Owl" (Meadow's Epic, base $16/s). New
  `FirstEggBiome = "Meadow"` and `FirstEggHatchSeconds = 5`.
- **PenService.acceptDelivery:** the player's first delivered egg, when it comes from Meadow (the tutorial nest),
  becomes a starter egg:
  - it is set to hatch an Owl, with a 5 s hatch so the tutorial's Hatch step stays quick (a normal Owl egg takes ~75 s);
  - it is flagged `Starter = true` (new optional EggRecord field).
  - A first egg from any other zone rolls normally. Every first egg still gets the +0.5 size bonus.
- **PenService.hatch:** starter eggs hatch with no traits: Mutation nil (whatever the egg variant looked like) and
  size Tier nil.
- **Income** (base, before rebirth/pass multipliers): $24/s at the smallest possible first egg (size 1.3),
  ~$31/s at 1.5, ~$69/s at 2.5. The tutorial's next step (save $100 for the first wheel) takes a few seconds.
- Compile-checked Economy, PenService, DataService. Not playtested.

### 2026-10-13 — Panel rotation is back, through a CanvasGroup rotor
- **Archive:** `ServerStorage.Snapshots_PreCanvasRotor` holds the no-rotation versions of ModalFx, SettingsController,
  RebirthController and ShopController. Each copy has an `OriginalPath` attribute; to roll back, copy its Source over.
- **ModalFx:**
  - `ROTATE_PANELS = true`. While a menu is open, ModalFx moves it into a transparent CanvasGroup `_FxRotor` and
    rotates the rotor; the menu itself never rotates.
  - The rotor has the same centre as the menu plus `ROTOR_MARGIN` 0.35 of extra room per side.
  - The menu inside keeps its exact on-screen size and position (checked within 1 px on a test copy of Menus).
  - The rotor carries the -8 deg open swing, the 3 deg pointer tilt, the +5 deg close twist (plus a fade) and Rebirth's
    rise.
  - The menu instance and all paths below it are unchanged. `finalize` moves the menu back before destroying the rotor.
  - `s.home` holds the real parent; the dim, particle layer, Rebirth vortex and Shop coin rain use it.
- **No rotor (and no rotation)** when Settings.ReducedFx is on, the profile never rotates (Shop), or the menu holds
  ViewportFrames. A ViewportFrame added after open drops the rotor and the panel settles flat.
- **SettingsController:** `TILT_DEG` back to 3, now aimed at the rotor.
- **Kept from the previous fix:** buttons and cascade rows that clip something inside still don't tilt (they can't
  be CanvasGroups).
- **Unverified:** Edit mode draws a CanvasGroup like a plain Frame (no clipping, no grouping), so the clipping inside a
  rotated rotor has to be confirmed in a playtest. Known CanvasGroup risks: slightly softer text, and reports of
  CanvasGroups not rendering on some phones. If either shows up, set `ROTATE_PANELS = false`.
- Compile-checked all four scripts. Not playtested.

### 2026-10-13 — Menu clipping fix: panels no longer rotate
- **Cause (tested in Edit mode with a temporary test GUI, since deleted):** Roblox turns clipping off for every
  descendant of a GuiObject whose Rotation is not exactly 0. That covers ClipsDescendants frames and
  ScrollingFrames alike, and even 0.0001 degrees does it. ModalFx rotated whole panels:
  - -8 deg swing on open
  - up to 3 deg pointer tilt the whole time the menu is open (desktop)
  - +5 deg on close
  So list rows, sparkles and bar fills drew outside the menus.
  - The old per-list CanvasGroup wrap (`_FxListClip`) only covered a direct child called `List`, and skipped lists
    with size constraints or UIScale.
  - Edit-mode screenshots never showed a CanvasGroup clipping anything, so it isn't a reliable fix either.
- Rotated CHILDREN inside an unrotated clipper are clipped fine: `StarterGui.ClipsDescendantsSupportsRotation` is on in
  this place (Roblox default since 2026-09-24; becomes permanent in 2026-11).
- **ModalFx:** new `ROTATE_PANELS = false`, so every session is `noRotate`:
  - no open swing, no tilt rotation, no close twist
  - the scale spring, jelly, rise and parallax are kept
  - `clipList` is skipped when nothing rotates
  - `ModalFx.stagger` no longer tilts rows that clip something inside them (e.g. QuestRow.Bar)
- **SettingsController:** `TILT_DEG = 0` (its own copy of the tilt).
- **UI:** `bindButton` hover/press tilt and the `UI.nope` wiggle skip buttons that clip something inside them
  (Buy/Claim shine holders, ClipsDescendants buttons whose big ripple would spill out).
- Compile-checked ModalFx, SettingsController, UI. Not playtested.

### 2026-10-12 — Speed Packs: no more exponential growth
- **Problem:** packs granted `max(Base, Speed * Pct)`. Past the second-to-last gate (400k) nothing
  capped it, so repeat buys multiplied Speed (L = x3 per buy: 1e7 -> 3e7 -> 9e7 ...).
- **Fix (`Config.Products`):** new `Products.ZoneSpan(speed)` = next gate minus the gate you passed
  (the last zone's width, 2.1M, once every gate is open). Packs now grant `max(Base, ZoneSpan * Pct)`;
  the "never skip more than one zone" cap is unchanged. The amount depends on your zone, not your
  Speed, so repeat buys add a fixed amount (endgame: S +525k, M +1.575M, L +4.2M per buy).
  - Checked: 50 L packs from 0 reach 194M, growing by a flat 4.2M per buy after the last gate.
- Pack descriptions are now "+25/75/200% of your zone's Speed span." (Creator Hub descriptions
  still say "% of your Speed"; update them there.) MonetizationService header comment updated.
- Compile-checked Products + MonetizationService. Not playtested.

### 2026-10-12 — Look pass 1: Steal an Egg style (HUD numbers, floors/walls, Shop)
- Style source: user skill `roll-egg-art-direction` (values measured from the Steal an Egg decomp).
- **HUD:** `Hud.Stats.Speed/Money` lose their backing panels and strokes. `Value` labels are now
  layered: black Rubik Bold Italic base with a 2 px black stroke, plus a coloured child `Shadow`
  (speed `0,221,255`, money `38,255,0`) shifted up for a 3D look. `Income` is white Rubik with a stroke.
  - `UI.syncLayers(root)` keeps `Shadow`/`White` children's Text in sync (called in HudController bind).
  - `UI.face(label)` returns the coloured layer; ShopController's red money pulse uses it.
  - Speed counter shows just the number (dropped "Speed … (N ws)").
- **World (restyled in place, nothing moved):**
  - Floors: Meadow/Riverbank/Canyon `Ground` + `Lobby.Ground.LobbyFloor` → Plastic + MaterialVariant
    `Studs`, smooth top, checker Texture `StyleChecker_Top` (6372755229, black, 0.8, 8 studs).
    Saturated colours: Meadow/lobby `63,165,0`, Riverbank `30,160,70`, Canyon `214,146,52`.
  - Walls: lobby walls (terracotta `201,112,78`) and Canyon brick walls → SmoothPlastic + `2022 Stud`
    + checker textures (6905003203, 0.9) on the side faces. Trims/courses/bands/caps → `2022 Stud`,
    lobby wall trims lime `70,200,40`.
  - Every touched part has a `PreStyle` attribute (JSON: material, variant, colour, top surface) for a revert.
- **Shop menu:** panel gets a `Skin` CanvasGroup (dark fill, inlet, diamond pattern, bevel images),
  `DropShadow`, 4.9 px black outline, 10 px corners. Title tab is now bare white Rubik text with a
  3.3 px black stroke (tab fill/stroke/shine hidden; names kept for ShopController). Close is a red
  gradient square with a black outline.
  - `Templates.ShopOffer`: grey gradient card (`BodyGradient`), inlet texture, `Border` frame with a
    2.8 px black stroke, Rubik text with black strokes, cash-green Buy.
  - `UI.Theme.Cash` → `0,255,21` (affects every cash button).
- Verified by edit-mode screenshots and `loadstring` compile checks only. Not playtested.

### 2026-10-12 — Guards retake dropped eggs; secured eggs; big stage-scaled luck
- **Secured eggs:** the first time a carried egg crosses into the bases it becomes `WorldEgg.Secured`
  (attribute `CarrySecured`).
  - Secured eggs are never hunted again: no guard wake on pickup, no RunRed vignette, no gold-line pulse.
  - The "SAFE!" banner plays once, on securing.
- **Guards retake dropped eggs:** `Grabbed` now passes `secured`. A never-secured egg picked up anywhere
  outside the bases wakes its guard (it used to need to be inside its own biome), so guards come to
  take dropped eggs back.
- **Luck:** egg luck = `Biome.LuckBase` (Meadow 5, Riverbank 25, Canyon 125) × size luck
  (`1 + 0.75 × (size − 1)`, was 0.5) × variant.
  - Drop odds use luck relative to the stage base (`Curves.LuckAdjustedTable(table, luck, base)`), so
    the big numbers don't flood the rare pets.
  - `Curves.FormatLuck`: x12.5 / x312 / x1.2K.
  - Eggs already placed keep their old (small) luck values; their pet was rolled at placement.

### 2026-10-12 — Eggs grow wherever you walk
- `EggService.carryTick`: every stud walked while carrying grows the egg, anywhere (capped per sample at
  the expected walk speed; no growth while airborne or teleporting).
  - The own-zone / homeward-progress / kiting rule and `Carry.MinX` are removed.
  - The hamster wheel grows the egg without walking (the larger of walk and wheel counts).
  - The exponential slowdown (`Curves.GrowthFactor`) still applies everywhere.
- Carry hint: "Growing as you roll! Guards stop at the gold line".

### 2026-10-12 — Eggs never lock; exponential growth slowdown; grow eggs in the hamster wheel
(Not playtested.)
- **No more lock:** the lock line is now the SAFE line. `EggService` keeps `Carry.Safe` (both directions,
  every frame), mirrors it as player attribute `CarrySafe`, and fires `EggService.ReachedSafety` on
  entering the bases. `Finalized`, `CarryFinal`, `CarryMax` and `IsFinal` are gone; `IsSafe` is new.
  - GuardService and TutorialService listen to `ReachedSafety`; guards still give up there.
  - `Grabbed` no longer passes `final`.
- **Growth:** an egg grows in its own zone (homeward progress / kiting) and in your own hamster wheel.
  - The rate is multiplied by `Curves.GrowthFactor(size, start, softCap)` =
    `exp(-3 × (size − start)/(softCap − start))`: ~5% at the per-egg soft cap, never zero, no hard cap.
  - `WorldEgg.StartSize` is new. The attribute `CarryGrowth` (0..1) is shown as "growth N%" on the
    Carry card; the egg label dims as it slows.
- **Wheel rolling:**
  - `SpeedService` now lets egg carriers run in the wheel (Speed gain + `OnWheel`).
  - `EggService` credits `Curves.WheelRollRate(walkSpeed, tier)` studs/s, scaled by
    `Economy.Egg.WheelGrowthFactor` 0.15 and +25% per tier.
  - `CarryController` keeps the egg inside the wheel and spins it at walk speed.
- **Walking animation:** new `WheelRunController`. In the wheel your character faces the running
  direction (deck X) and plays its own walk animation (from `Animate`, R15/R6 fallback), sped up with
  WalkSpeed; it replicates to others. `WheelFxController` already spins the wheel.
- **Text:**
  - The Meadow gold-line floor text now reads "SAFE ZONE".
  - Tutorial Escape/Home copy is updated.
  - The HUD banner says "SAFE!".
  - Carry hints cover the wheel, the zone and the safe/plot cases.
- **Placement:** any carried egg can be placed on your plot (no lock needed).

### 2026-10-12 — Juicy Shop (multi-agent build)
(Not playtested.) The plan is `design/features/shop-juice/design.md`, built by parallel Sonnet and Haiku agents
and then reviewed and fixed by agents.

**Instances**
- New in Menus: `ShopBackdrop` (dim, click to close), `ShopFx` (confetti/coin layer).
- New in Shop: `Sparkles`, `IconRays`, `TitleTab.ShineHolder.ShineBar`.
- `Shop.Active = true`; List right padding 14.
- `Templates.ShopOffer` got:
  - root `UIScale` and `HoverStroke`;
  - `IconGlow`, `Pips`(+`PipTemplate`), `Buy.ShineHolder`, `Buy.Progress.Fill`;
  - `NeedMore` (overlays Buy), `Stamp`, `Flash`, `MaxedRays`.

**New libraries**
- `Client.UIFx` (motion): shake, wiggle, flash, shine/shineLoop, stamp, spin, breathe, colorPulse,
  slideSwap, bob. It keeps a shared baseline per (instance, property), so overlapping effects never drift.
- `Client.UIFxParticles`: confetti, burstRing, coinFly, floatSparkles, starPop.
  - One pcall-guarded RenderStepped loop.
  - Caps: 250 burst particles, 60 ambient.
  - Waits for layout before spawning.

**`Client.Controllers.ShopController`**
- Owns the Shop via the new `HudController.SetModalHandler` / `CloseModal` / `IsModalOpen`.
- **Open:** a staged timeline of backdrop + blur, elastic panel, title drop, icon spin + rays, close
  pop, staggered cards with pips filling, shine loops and sparkles.
- **Idle:** reactive Refresh (affordable = green + shine + breathing stroke + bobbing coin; otherwise
  grey + progress bar), an "unlock" beat when an offer becomes affordable, and hover glow / wiggle.
- **Buy:**
  - Can't afford: shake, red price, "Need $X more", Hud money wiggle.
  - Success: pop, flash, confetti, ring, coins flying to the Hud money, a stamp, the pip filling, an
    icon spin, the text sliding to the next tier, the price rolling, a panel shake, and the maxed reveal.
  - 2.5 s timeout counts as failure. In-flight purchases survive a close/reopen.
- **Close:** staged reverse animations, tracked tweens and loops cleaned up, and reopen during close is
  safe.
- Sounds referenced (skipped until added): Open, Purchase, Coin, Error, Unlock.

### 2026-10-12 — Treadmills → hamster wheels (drop from the sky on first purchase)
(Not playtested by Claude.)
- **Asset:** Creator Store 9696414411 "Hamster Wheel" by Sgt_Pacman, inserted into Quarantine.
  - Audit: 2 unions, 1 hinge, 2 attachments, no scripts.
  - Cleaned copy `ServerStorage.DecorLibrary.HamsterWheel`: hinge removed, anchored, non-colliding,
    `UsePartColor` on, scaled 1.25× (radius 6.8, axle height 8.4).
  - `Pivot` part at the axle, with X = axle.
  - Also inserted and rejected: 8767556431 (56 studs tall).
- **Old visuals:** the `TreadmillVisual` models moved out of the plots to `ServerStorage.Removed_Treadmills`.
- **Tiers:** `Economy.Wheel` tiers renamed Wooden / Shell / Speckled / Golden / Crystal / Cosmic Wheel.
  Each has Color / Material / Transparency / StandColor and re-skins the wheel.
- **`SpeedService` syncs a wheel model per plot every 0.25 s from the owner's `WheelTier`:**
  - None at tier 0 (players start without one).
  - Spawned on the porch deck (axle across the deck, rim on the deck top).
  - Re-skinned when the tier changes (`Tier` attribute); removed when the plot empties.
  - The `WheelPad` deck only collides once a wheel stands there.
  - `EconomyService` calls `SpeedService.OnWheelBought`; tier 1 marks `DropAt`.
- **New `WheelFxController` (all clients):**
  - Drop from 55 studs, fading in and spinning, then a dust ring, a bounce, and sparkles.
  - The owner's camera shakes slightly.
  - The wheel spins around its axle while the owner runs in it (`OnWheel`), easing up and coasting
    down, faster on higher tiers.
  - Hop and sparkles on upgrade.
  - Sounds `WheelLand` / `WheelUpgrade` are skipped until they exist.
- **Text:** boards read "HAMSTER WHEEL"; the board says "Buy a hamster wheel"; the deck label and
  tutorial text mention the hamster wheel.
- **Static fit check:** a test wheel on all 5 porches overlaps nothing.

### 2026-10-12 — Speed gain fly-in + bouncy UI
(Not playtested by Claude.)
- New `SpeedFxController` owns the Speed counter. Every gain spawns a "[shoe] +N" popup
  (`Hud.Templates.SpeedPop`) at a random spot. It bounces in, holds 0.22 s, then flies along a
  quadratic bezier (bowing sideways and up, shrinking, accelerating) into the bottom-left Speed icon.
  - The counter only counts up when a popup lands: elastic pop on the label, icon and panel, plus a
    ring burst (`Hud.Templates.SpeedBurst`).
  - Treadmill ticks are batched into one popup per 0.6 s. A gain ≥ 20% of current Speed launches at
    once. At most 6 popups in flight. Decreases (wipe) snap instantly.
  - Popups render in the new `Hud.FxLayer` (outside the Stats CanvasGroup, so nothing clips).
  - The arrival sound is named `SpeedGain` (silently skipped until the sound pass adds it).
- `Client.UI`: new `UI.Pop` (Back) / `UI.Bouncy` (Elastic) TweenInfos, plus `UI.pop(inst, peak)` and
  `UI.popIn(inst, from)` with one live tween per UIScale. Buttons now overshoot on hover and spring
  back elastically on release.
- `HudController`:
  - toasts bounce in and shrink out;
  - the Carry card bounces in when you grab an egg;
  - the money counter pops on every income tick;
  - the Eggs "ready" badge pops when the count rises;
  - its old Speed handler was removed (`SpeedFxController` owns the counter now).

### 2026-10-12 — Placed eggs keep size, guard chases to the lock line, hatch sequence, hotbar pets
(Not playtested by Claude.)
- **Egg size:** `Curves.EggDiameter` is logarithmic above Size 1: `2.5 × (1 + 0.9 ln Size)`, still
  capped at 12 studs (`Economy.Egg.DiameterLogScale`). Placed eggs use the exact rolled diameter (the
  old 5-stud cap is gone, in `PenService` and `PlacementController`).
- **Guard:** no zone leash. It follows you through any zone until you cross the lock line (that also
  locks the egg; `Finalized` → give up). Backstop: `WorldIndex.PastLockLine`.
- **Placing eggs:** the server tags a just-placed egg with `DropAt` and `Luck`. New `PlotEggController`
  (every client, every plot) drops it from 40 studs while fading in, squash + dust ring on landing,
  then ticks its luck tag (clover + xN.NN) up from x1.00. The luck tag stays on every placed egg, for
  everyone.
- **Hatching:** the pet is saved at once; the egg part stays 2.4 s with `HatchPetId/HatchPetSize/
  HatchOwner/HatchAt` so all clients play: shake left-right-left → shell shards → pet pops up → hovers →
  quadratic bezier into the owner (shrinking) → sparkle. Then the reveal card + a hint toast.
- **Hotbar pets:** hatched pets arrive unplaced as Tools (`PenService.syncTools`; re-issued on
  respawn, after sell/unplace/Equip Best/wipe). New `PetToolController`: a ghost of the pet over your pen
  (green/red); click/tap sends `RequestPlacePetAt(uid, pos)`. The server checks it's inside the pen,
  within reach and slots free, then saves pen-local `PenX/PenZ` (the pet spawns there).
  Grabbing an egg unequips tools.
- New `Shared.PetLook` (placeholder pet body used by the pen, the tool handle and the hatch effect).
- `Hud.Carry` moved up 72 px so the Roblox hotbar doesn't cover it.

### 2026-10-12 — Admin panel, clover luck icon, inflated egg numbers, random caps, egg variety
(Not playtested by Claude; the user playtests and reports.)

**Admin panel**
- New `StarterGui.Admin` (Toggle "ADMIN" top-left, centred Panel: title tab, close, player-name box,
  red WIPE DATA button, status line). Built in Studio; `AdminController` binds it.
- New `AdminService`: admins = `ADMIN_USER_IDS` (1570336050), the place owner, and any Studio test
  session. Sets `IsAdmin` on the player so the client shows the toggle; the server re-checks every request.
- New remote `AdminWipe(targetName?)` (blank = yourself; exact or unique-prefix name match), rate-limited
  2 s. Wipe = drop/return the carried egg, `DataService.Wipe` (resets the profile to the template in place
  and re-replicates every key), rebuild plot eggs/pets, speed and treadmill label, teleport home.
  Tutorial restarts via new `DataService.Wiped` signal. Two clicks within 3 s to confirm.

**Luck = green clover icon**
- `Icons.Luck` = rbxassetid://118734496887081 (Creator Store decal 123407885565213 "Clover", public
  domain, cartoon four-leaf clover with black outline; audited copy in Quarantine).
- The word "Luck" is gone from the UI: rolled-egg billboard (weight on top, clover + x1.50 below),
  Carry card (`Hud.Carry.Info.LuckRow` = Clover + Stats, flex layout) and the Eggs list row
  (`Menus.Templates.EggRow.LuckRow` = Clover + Sub).
- Pulse while walking: the egg billboard beats on every half-turn of the egg and pops when the number
  goes up; the clover wiggles. The Carry card's clover breathes while you walk (stronger while growing).

**Inflated egg numbers**
- Size is now shown as a weight: `Curves.FormatWeight(size)` = `100 × size^2.2` kg, with commas
  ("2,350 kg"; "190K kg" above 100,000). Used on the billboard, Carry card, prompts, Eggs list, Pets
  list, pet labels, hatch card, LOCKED IN banner, placed toast and the Biggest Egg board.
- `GrowthPerStud` raised: Meadow 0.012 → 0.018, Riverbank 0.01 → 0.015, Canyon 0.009 → 0.013.

**Random caps**
- Every nest egg rolls its own cap: `zone MaxSize × (0.45 + 1.30 × u^2.4) × variant` (`Economy.Egg.Cap`,
  `Curves.RollEggCap`). Most caps are low, a few are huge. Revealed on grab (`CarryMax`): Carry card shows
  "max N kg", then "MAX!"; the billboard shows "MAX!".

**Egg variety**
- New `Shared.Config.EggVariants`: Plain 44, Speckled 22 (tinted speckle texture), Spotted 14 (Pebble),
  Marbled 10 (Marble), Shiny 6 (Glass + glow), Golden 3 (Foil gold + glow), Rainbow 1 (hue-cycling +
  glow). Rarer = bigger start, higher cap, more luck (`Curves.EggLuck`). Plus 4 tints per zone palette
  and a random start size (0.8–1.3, `Curves.RollStartSize`).
- Eggs are now a real egg mesh: `ReplicatedStorage.Assets.Egg` (Creator Store MeshPart 739816168,
  audited, no scripts). New `Shared.EggLook` builds/styles every egg (nest, ground, rolled, plot) from
  (BiomeId, Variant, Tint). New `EggFxController` cycles Rainbow eggs (tag `RainbowEgg`).
- Variant/tint are saved on the egg record (`Variant`, `Tint`) so plot eggs keep their look; prompts read
  e.g. "Golden Meadow Egg".

### 2026-10-12 — Rotated map support, lock line, porch treadmill + upgrade boards, fences
- User rotated the whole map 90° (home = world +Z) and opened the entrance; World must never be rebuilt
  (`GrayboxBuilder.Build` now refuses unless passed `true`).
- `WorldIndex` rewritten around a corridor frame (`ToLocal`/`ToWorld`, `HomeDirection`, `LockLineX`,
  `PastLockLine`, `OnTreadmill`); EggService/GuardService/GuardFx/Tutorial use it instead of world X.
- Egg locks when the carrier crosses the lock line (bases ↔ first zone), not when leaving its own zone.
  Meadow ZoneEdge reads "<<< CROSS TO LOCK IN" and pulses gold while your egg is unlocked.
- `Hud.RunRed` (user-made) pulses during the run (faster while chased). No jumping while rolling
  (server zeroes JumpHeight, client disables the Jumping state). Egg billboard centred on the egg.
- Lobby decor moved to `ServerStorage.Removed_LobbyDecor` (minimal lobby).
- Plots: fenced (Toolbox FenceSegment, 10-stud gate), porch with the treadmill outside the fence
  (Toolbox treadmill visual, invisible WheelPad deck), SurfaceGui upgrade boards (PenBoard,
  TreadmillBoard) driven by `PlotBoardService` + `PlotBoardController` (your own boards' SurfaceGuis move
  into PlayerGui so they're clickable). Board click-to-buy not verified yet.
- Zone props: CanCollide/CanTouch/CanQuery off (440 parts).
- Security: user-added "Hamster Wheel Stands" contained a backdoor (`LightConfig` → `require` of asset
  90983637061475, live servers only). Moved, disabled, to `ServerStorage.Quarantine._BACKDOOR_HamsterWheelStands`.

### 2026-10-12 — 5 plots (two sideways), mirrored stages, Toolbox decor

**Plots: 7 → 5, U-shaped like Steal an Egg**
- `ServerStorage.DevTools.MapLayout` rewritten for plots: each plot now has a `CFrame` whose local +X
  is its open side. Plots 1–3 = back row at X −110 (Z 0, −50, +50) facing +X (the arch). Plots 4/5 =
  sideways at X −36, Z ∓105, facing the plaza (+Z / −Z). New fields: `CFrame`, `SpawnCFrame`;
  constants `BackRowX`, `BackRowSpacingZ`, `SidePlotX`, `SidePlotZ`, `LobbyMinX/MaxX/HalfZ`.
  Removed `PlotCenterX`, `PlotSpacingZ`, `MaxIncubators`.
- `GrayboxBuilder.buildPlot` places everything in the plot's frame (floor, fences, pen, wheel, sign,
  spawn). Spawn part's LookVector = out of the plot.
- `Shared.WorldIndex`: new `SpawnFacing` (from the Spawn part) and `PenCFrame`; header documents that
  plots may be rotated.
- `PlotService`: characters spawn facing `plot.SpawnFacing` (was hard-coded +X).
- `PenService.randomPenPoint`: pen-local (works on rotated pens).
- Already rotation-safe (checked): free egg placement (floor-local), `WorldIndex.InPlot`,
  `PlacementController.validate`.
- Hub re-laid out: street serves the back row only; two side paths run from the side plots to the
  plaza; statue X = side-plot X (−36) so side plots face it. Shop kiosk and Biggest Egg board moved
  next to the arch (x −16, either side of the avenue).

**Stages mirrored**
- New `MapLayout.Stage` = one nest + sleeping-guard layout used by every zone:
  nest centre `EndX − 40`, lane Z 38, 5 nests on a radius-6 ring, guard post at nest centre + (14, 0, 8).
  Verified: every biome has Nest1 at (−34, 38) and GuardPost at (−26, 46) relative to its far edge.
- `Shared.Config.Biomes`: removed per-biome `NestCount` / `NestDepth` (header explains the shared layout).

**Decoration from Toolbox**
- Inserted into `ServerStorage.Quarantine` and audited: no scripts in any; Humanoids/Values stripped.
  - Low Poly Nature Pack — Proudism (9682467046)
  - Cattails — gaspastrike (1784440735)
  - Hay Bale Huge — QuDTact (10157955542)
  - Medieval Pack: Bench and Street Lamp — LokisGamerLobo (119226124827525)
  - Cactuses — Perfectuary (16995096640)
- New `ServerStorage.DecorLibrary` (21 models, anchored, pivot = bottom centre, attrs Height/Footprint):
  TreeRound, TreeAcacia, TreePine, TreeCherry, Palm, DeadTree, Bush, BushFlat, GrassTuft, GrassSmall,
  Flowers, Mushrooms, Log, RockBig, RockTall, RockMed, Cattails, HayBale, Bench, LanternPost, Cactus.
- `GrayboxBuilder`: new `decor()` / `turnToward()` / `lantern()` / `bench()` helpers.
  - Hub: `buildHubDecor` replaces part trees + part lamps — groves in the back corners, pines on the
    east wall, flower beds, lantern posts along the avenue + side paths, 4 benches facing the statue.
  - Meadow: round + cherry trees, hay bales, bushes, flowers, grass, rocks.
  - Riverbank: stream strip (part, gameplay-readable), cattails on both banks, rocks, logs, pines, bushes.
  - Canyon: sandstone-tinted rock mesas, cacti, dead trees.
- World rebuilt: 1007 parts, 11 lights, 196 decor models. Previous world backed up to
  `ServerStorage.WorldBackup_2026-10-12`.

**Verified (playtest)**: no new errors; plot 1 spawn faces +X; side plots' spawn + pen corners pass
`InPlot`; side plot 5 → Meadow nest ≈ 14 s at walk speed 16 (7.9 s at 27.8).

**Found, not ours (left in place)**: `Workspace."Sell Stands"` (a copy of the parked reference asset,
sits on the north side path at (−40, 39)) and `Workspace."Hamster Wheel Stands"` (floating at y 78).
Both script-free. Ask before removing; "Sell Stands" must not ship (reference rule).

### 2026-10-12 — Menus built in StarterGui (Steal an Egg positioning)
- New `StarterGui.Hud`: Stats (speed above money, bottom-left), Left (Shop pill, left-centre),
  Tabs (Eggs/Pets squares, right-centre; Eggs has a ready-to-hatch badge), Timer (bottom-right; moves
  top-right on touch devices so it isn't under the jump button), Carry (info card + Drop, bottom-centre),
  Run/Lock banners, Toasts (top-centre), Tutorial bar (top-centre), Templates (Toast, HatchCard with
  rarity-tinted sunburst).
- New `StarterGui.Menus`: Shop (centred modal, title tab + icon overhanging top-left, close top-right),
  Eggs / Pets docked right-centre panels with left-edge close tabs, Equip Best under Pets, Templates
  (Empty, EggRow, PetRow, ShopOffer). Empty states added.
- `Client.UI`: `bindButton`, `clone`, `applyIcons`. `HudController` rewritten to bind (no code-built
  screen UI). `TutorialController` binds to `Hud.Tutorial`.

### 2026-10-11/12 — Hub polish (walled, readable)
- Hub walled (cobblestone + timber trim + posts + invisible barrier); one gap under the entrance arch
  ("TO THE NESTS >>>"); paving only where you walk; chevrons to the arch; golden egg statue on the plaza;
  Biggest Egg board (`PlazaService`); Shop kiosk with a Custom prompt (`OpenModal = "Shop"`).

### 2026-10-11 — Gameplay notes pass
- Click/tap to place eggs anywhere on your plot (`RequestPlaceEgg`, ghost egg, server validation:
  reach 45, edge margin, wheel radius, spacing, max 6).
- Zone exit checked every Heartbeat (~60 Hz) with ping grace `min(WalkSpeed × ping, 6)`; leaving the
  zone finalizes the egg (no re-rolling growth), guard gives up, LOCKED IN banner.
- Custom proximity prompts (Toolbox "Proximity Prompt Customizer", Bitwise Operations, audited) with
  hold times: Steal 0.6 s, Pick up 0.3 s, Hatch 0.5 s; themes Default / Steal / Hatch.
- 7-step tutorial (`TutorialService` + `TutorialController`) using the open-source
  `ReplicatedStorage.TutorialArrow` beam.
- HUD icons from the open-use reference images via `Shared.Config.Icons`.

## Open items
- Toolbox sound pass for `Shared.Assets.Sounds`; low-poly meshes for guards, pets and the carried egg.
- Plots themselves are still bare (fences/floor are parts). Consider a Toolbox fence + a plot-edge bush row.
- Guards walk through props (no pathfinding).
- Before publishing: delete `REFERENCE_PARKED`, `Quarantine`, `WorldBackup_*`, and the stray Workspace stands.
  Consider removing the `RunService:IsStudio()` admin grant only if Team Test with untrusted editors.
- Placement ghost is still a sphere (ForceField); could switch to `EggLook` mesh.

---

## Research — Steal an Egg (raw text)

Raw text of `steal-an-egg-research.html` (the teardown artifact: https://claude.ai/artifact/MAhNhAvKyDcWYoTao8CpNX).
Design references only — use for mechanics shapes, pacing and lessons, never for copying assets/code.

```text
Steal an Egg Teardown

Roblox · design research · data as of 11 Oct 2026

 Steal an Egg

 How the July 2026 breakout works, where players actually spend their time, and what keeps them or loses them at each stage. Written as a reference for building a game in the same genre.

0.96–2.0Mplaying on 11 Oct (varies by hour)

 7.68Bvisits

 92%like ratio (3.41M / 294K)

 7.46Mfavorites

 ~15 minaverage session (Rolimon's est.)

 7players per server, no private servers

Live numbers come from Roblox's public games API on 11 Oct 2026 (created 25 Jul 2026, genre Simulation › Tycoon); two pulls the same day read 2.01M and 0.96M playing. The all-time peak is 14.29M on Saturday 19 Sep (Rotrends and Rolimon's), during an Admin Abuse event with a Steal a Brainrot crossover. Weekdays sit around 1.7–2.5M. Session length is 12.6–15.2 minutes depending on tracker. The game is run by Misfits Gaming Group with partner Do Big Studios; the Roblox group owner account is "misfitsbsthree" (display name "ben").

Core loopWhere time goesHow new players arriveFirst session, minute by minutePlayer timelineMechanics tableMonetizationUpdate historyScreenshotsLessonsSources

01 · The pitch in one line

 Run in, grab an egg, outrun the guardian, hatch it, get richer, get faster, go deeper.

Grab egg from a sleeping guardian→
 Sprint home (guardian + players chase)→
 Place in pen, wait for hatch timer→
 Pet earns $/sec→
 Spend on treadmill, trails, pen slots→
 More Speed unlocks the next biome↺

Two numbers drive everything. Speed decides which of the 13 biomes you can rob and survive. Money per second decides how fast you can buy more Speed. Speed comes mostly from a treadmill in your base that you can stand on while AFK. The 5-minute egg reset keeps the map refreshing, and the Saturday "Admin Abuse" live event plus a new update almost every week keep the long tail alive.

 The structure is borrowed directly from Steal a Brainrot (steal from a base, earn passive income, defend, upgrade), crossed with egg-hatching pet sims. Steal an Egg's twist is that the thing you steal from is an animated guardian creature, so every run is a short chase with a clear pass/fail instead of a menu purchase.

02 · Where the hours go

 What players spend their time on

 Ranked from what I saw in three long YouTube playthroughs (a first-time session, a fresh-account restart, and a "100 days" run) and the guides cited below. The bars are a relative judgment of time share, not measured telemetry.

Waiting on timers

Standing on the treadmill to grind Speed (AFK-friendly), waiting on hatch timers that run from 10 seconds (Chicken) to 20 hours (Royal Skywhale), and waiting for the next 5-minute egg reset or the night phase, when eggs grow about 30× faster. This is the bulk of clock time. It is also where the removed "Reels" video feed lived, because players were idle on the treadmill anyway.

Steal runs

Short bursts of 10–40 seconds: pick the egg, sprint past the guardian, dodge traps and bats, drop it in the pen. They repeat every reset, and players sprint for any rare spawn the server announces. These are the moments players actually remember and the moments YouTubers film.

Base and collection management

Placing pets, pressing Equip Best, selling weak pets when the pen fills up, claiming Index rewards for each new species, feeding three copies into the Fuse Machine, and applying mutation crystals or bottles to eggs before hatching. Late-game players stockpile unhatched eggs for a big "opening" session.

PvP harassment and interception

Hitting egg carriers with a bat or laying bear traps (5-second root) so they drop the egg, then grabbing it. Experienced players scout other players' Speed and treadmill before picking a target. Server-hopping to find a lobby where you are the fastest player is common.

Scheduled events

The Saturday Admin Abuse (30–60 minutes of stacked luck, growth, money and Speed boosts, free treadmills, rare spawns). Recurring in-update events: a boss portal every 30 minutes (Dr. Scramble), a Nitro Race every 30 minutes, Capture-the-Egg holds, star drops at night for mounts.

Shopping and planning

Choosing between pen slots, treadmill levels and trails; buying Robux shortcuts (Speed packs, instant hatch, limited eggs). Short in time, but most of the game's decisions happen here.

03 · Acquisition

 How new players arrive

 The first audience came from Roblox's own charts and recommendations. YouTubers arrived after the climb was already under way. Every first-time creator in our sample explains why they're playing by citing the trend or the player count; none mention an ad or a friend. There is no public evidence either way on paid Sponsored ads. The operator, Misfits Gaming, describes itself as a studio that acquires and scales Roblox games, so early ad seeding is possible but unconfirmed.

Date (2026) | Signal | What happened | 

25 Jul | Launch | Game created. | 

 28 Jul | 75 CCU | First Rotrends data point. | 

 7 Aug | Up-and-Coming #1 | Enters the Up-and-Coming sort (stays until 22 Aug) and Top Playing Now. The earliest first-time video we found (Bemmy) shows about 13K players. | 

 12–13 Aug | Top Trending #1 | Top Earning on 12 Aug. Most Popular and Top Trending (#1) on 13 Aug. | 

 14 Aug | 85K CCU | First Rolimon's data point. Average session 17–21 min in mid-August. | 

 16–20 Aug | Creator wave | First-time videos in Spanish, Portuguese, Tagalog, Vietnamese and German, plus Foltyn's (4.9M views). Creators load in to find 150K–260K players already there. | 

 21 Aug | 371K CCU | "#1 game on Roblox overnight", per Misfits Gaming on X. | 

 22–23 Aug | 547K CCU | First Admin Abuse plus Update 1. | 

 24–25 Aug | Takedown | Offline from 22:21 to 01:44 UTC. Returns without the Reels feed. | 

 29 Aug–4 Sep | Meme wave | Comedy Shorts set in the game reach 15–21M views each, bigger than any gameplay clip. | 

 30 Aug | 1.33M CCU | Daily figure. | 

 4–10 Sep | Clone peak | 18 copycat games released in one week. 47+ in total by October. | 

 Saturdays | 7.6M → 14.29M | Weekly Admin Abuse peaks: 5 Sep 7.6M, 12 Sep 9.8M, 19 Sep 14.29M, 26 Sep 13.7M, 3 Oct 12.4M, 10 Oct 12.0M. Weekdays run 1.7–2.5M. The game leaves Top Trending on 26 Sep, and analysts report engagement falling 9–18% week over week. | 

What the store page promised before the click

Before 2 Sep: the icon showed a sneaking avatar holding an egg. Every thumbnail put a "YOU!" arrow on the player: sneaking past a tiger, grabbing a rainbow egg from a roaring yeti ("$89,121/s"), tiptoeing past a sleeping cosmic dragon. The description promised that guards chase you, that rare eggs spawn every 5 minutes and "make the whole server race", and that pets keep hatching while you're offline.

 From 2 Sep: a giant T-Rex egg held overhead, plus snake, yeti and shadow-dragon art. The icon switches every week to the newest egg, with a countdown ("24 HOURS", "3 HOURS") the day before Admin Abuse.

 The sneak-and-flee promise is delivered within the first minute. The "huge $/s" promise is not: the first pet earns $2–8/s, and the billions shown on the tile take days or Robux.

What Roblox measures for these players

 From Roblox's 2026 discovery documentation. Recommendations are ranked only on players who arrived organically, averaged per user. Players who arrive through Sponsored ads are excluded; ads "speed up consideration" and nothing more. In June 2026 Roblox extended the retention window from 7 to 28 days, saying shorter windows had over-rewarded eye-catching thumbnails.

Signal | When | What decides it in Steal an Egg | 

Play-through rate | Tile impression → play | "YOU!" arrow, monster, giant egg, $/s number, a self-explanatory title, and a million "playing" as social proof. | 

 Bounce < 60 s | First minute | Chase within 30 s of spawning. Risks: getting caught or bat-hit on the very first run, and a bright HUD full of Robux offers. | 

 Bounce 61–180 s | Minutes 1–3 | Instant first hatch, the golden "Mama" pet, money ticking up, free 10K Speed, the treadmill appearing. | 

 Playtime per user (capped at 60 min/day) | Session | Treadmill time, the 5-minute reset countdown, hatch timers, the biome ladder. | 

 Play days (day 1, days 2–7, days 8–28) | Returns | Offline hatching and income, multi-hour timers, Saturday Admin Abuse plus the update, the weekly icon countdown. Days 8–28 is where analysts see it weakening. | 

 Co-play days | Returns with friends | "Friend Boost +10%", friends carrying giant eggs, co-op bosses. | 

Rewarded video ads, which appear as "[AD] 2× Cash Reward" products in the game, need 100K+ average DAU to qualify. They are a way to earn money once a game is big, not a way to get players.

04 · First session

 First session, minute by minute

 Built from 15 first-time playthroughs uploaded 7 Aug – 2 Sep in eight languages, about 1,900 YouTube comments from early videos, and two mobile first sessions. Real-time pacing comes mainly from LvBlox (129 subscribers, raw footage, no Robux) and SkellyPlays (no Robux, after the takedown). Creators who spent Robux skip the middle of this timeline, so treat their pacing as the fast edge.

Before clickHome tile

The player sees a high-contrast tile showing a monster, a giant egg, a "YOU!" arrow and a $/s number, with a million people playing. Many have already seen the game in memes: the biggest Shorts were comedy skits, not gameplay.

Gets the click
A thief fantasy that reads without any text.
Social proof from the player count and memes.
Familiar genre: players describe it as "Escape Tsunami for Brainrots plus Grow a Garden".

Loses the click
Looking like the 47+ clones. Roblox may reduce discovery for near-identical titles and visuals.

Roblox reads play-through rate

0–60 sFirst chase

Spawn with 10 Speed, $0, and a bat and bear trap in the hotbar. A red arrow trail leads to a snoring chicken. Across videos, the first grab happens between 0:07 and 1:26, and the chicken wakes up and gives chase. Some players get caught on the first try (Tincho88 at 0:52).

Hooks
Under 30 seconds to the core fantasy.
Sound and comedy: the snoring, the "!!", the RUN!! banner.
Guardians that look scary but are beatable in the Forest.

Bounce risks
Another player bats or traps you within the first 2–3 minutes. Seen in Portuguese, Spanish, German and English videos.
The bright HUD and Robux pop-ups put off some players ("full of robux adds", from a commenter).
Mobile: the joystick sits under buttons, and other players' pets cover the screen.

Roblox reads bounce under 60 seconds

1–3 minFirst pet

The tutorial egg hatches in about 3 seconds into a golden chicken, bird or dog that calls the player "Mama". The joke landed in nearly every video in every language. The pet earns $2–8/s. The player claims the Index reward and the free gift (+10K Speed for like, favorite and join), and the treadmill appears through the tutorial and the $1K upgrade.

Hooks
Guaranteed lucky first hatch.
A pet with personality.
A big free Speed jump right away.

Bounce risks
$2/s next to other players' billions. A Turkish newcomer asked, "Is this made for new players?"
Players who skip the tutorial can't find the treadmill (BasicBlox searched until 3:28).
Mobile: tiny shop text.

Roblox reads bounce at 61–180 seconds

3–7 minTreadmill and first night

Before 25 Aug, standing on the treadmill opened the Reels feed. It took up roughly minutes 2–7 in videos by LvBlox and KreekCraft. Reactions ranged from amused shock ("They added TikTok to Roblox") to irritation (Kaydee: played Roblox to avoid TikTok). There were complaints about inappropriate clips and a forced camera zoom. An off switch existed in Settings, but most players never found it. After 25 Aug, this phase is AFK treadmill time and claiming Index rewards. The first night falls around 1:30–2:30 ("Why is this all blocked off?") and resets every egg.

Hooks
Passive progress: Speed climbs while you watch.
The first server-wide rare egg announcement at a reset.
The first steal from another player with the bat or trap ("feels great", LazySly).

Bounce risks
The night reset is never explained.
Bear traps don't work on guardians, which surprises players.
Getting griefed with no way to respond.

The pre-takedown treadmill: a vertical feed with likes and comments, a "+200 Speed" ticker, and a 3 R$ offer on the left.
 Roblox reads qualified session, playtime

7–20 minFirst gates and the pen

The player climbs the first gates: Lake (900), Desert (10K; LvBlox reached it at 8:50), Jungle (Pessego took the tiger egg at 57K Speed at 7:57). The pen fills at about 7 pets, so they learn Equip Best and Sell, and that eggs can't be sold. Fusing fails because it needs 3 identical pets. The first trail is bought with cash (Grey $100 ×1.5, Green $5K ×2, then a $1.5M trail). Robux offers start appearing: x2 Speed for 3 R$, instant hatch for 9–160 R$, Grow All, Speed packs. A Secret egg is announced that the player is too slow to reach ("somebody give it to me", LvBlox at 17:14).

Hooks
A new creature to beat at each gate.
Income jumps by orders of magnitude with each good hatch.
Rich players' giant pets on display ("now I'm motivated", Bemmy).
Other players gifting eggs (seen repeatedly in the Vietnamese video).

Bounce risks
"Recommended speed" is noticed late or judged wrong. Heavy eggs slow you down and the game never says so.
Dragging each egg to the hotbar.
Mobile: the cash and Robux buttons sit side by side, and one player bought the wrong Robux item.

Roblox reads playtime per user, spend day

20–40 minThe first wall

The next gates are Snow (170K) and Volcano (700K). Treadmill upgrades jump to $5M, then $3B, and a pen upgrade costs $75M. LvBlox only discovered treadmill upgrades at 26:26 ("I'm so dumb"). This is where the average 14–15 minute session has already ended, and where first-time videos stop at 10–17 minutes, often with "nothing more to do".

Hooks
Secret and Eternal spawns at resets.
Queuing a multi-hour hatch gives a reason to come back.

Bounce risks
"Takes way too long to get speed" and "not doing no progress".
Losing an egg at the last second near your base. Two creators rage-quit or threatened to (Bemmy: "I don't want to play this game"; KreekCraft: "never playing this game again").

Roblox reads playtime per user

40–60 minSpenders and free players split

Free players AFK on the treadmill and steal when they get the chance. After the takedown, a free player reached the 4th biome in about 20 minutes, stole an 8-hour Eternal egg at about 30 minutes, and reached 3.3M Speed at about 1.5 hours and 22M at about 2.5 hours (SkellyPlays). Spenders jump to Prehistoric (17M) and call Cosmic's 700M "a crime".

 Roblox reads playtime (capped at 60 min/day)

Day 1 → 28Will they return?

Day 1: offline hatching, timers finishing, the first rare egg lost to an exploiter or a faster player, "no private servers". Days 2–7: Saturday Admin Abuse plus the update, AFK overnight (mobile players loop gestures with iPhone Voice Control), the wall around 1–2.5B Speed. Days 8–28: pay-to-win realization, exploit fatigue, "so repetitive". There are no daily login rewards, streaks, quests or codes to pull players back.

 Roblox reads play days at day 1, days 2–7 and days 8–28; co-play days

The early first-time videos behind this timeline

Video | Lang | Date | Views | Robux | Why they joined | Key beats (video time) | Ending | 

Bemmy | EN | 7 Aug | 224K | Heavy, gifted pets | ~13K playing | 0:26 golden dog "mama" · 1:08 "YouTube shorts on this thing" · 1:30 first night confusion · 3:51 jealous of a whale, "now I'm motivated" · 11:37 Speed too slow · 14:16 egg stolen at the last second | "I don't want to play this game" | 

 BasicBlox | EN | 13 Aug | — | Yes | Trend | Skipped the tutorial, couldn't find the treadmill until 3:28 · 3:57 "I can't even turn it off" | — | 

 Gnoren Zero | ES | 16 Aug | 143K | ~5,000 R$ | Trend | 0:23 "the TikTok of Roblox" · 1:38 night reset · 2:35 no private servers · 6:35 2-hour timers need "Robux or lots of patience" · 10:57 caught in a trap | 17:28 | 

 LazySly | FIL | 17 Aug | 745K | No | — | 1:07 golden bird "Mama?!" · 1:27 treadmill, turned the videos off · 2:50 learns drop-and-regrab · 5:41 first trap on a player · 9:46 hatches a SECRET from a stolen egg | Excited, 11:03 | 

 Pessego | PT | 17 Aug | 44.5K | No | — | 0:23 golden chicken $2/s · 2:05 bat-hit by a player · 3:18 treadmill upgrade 15K · 7:57 tiger egg at 57K Speed · 12:00 pen full, upgrade costs 75M · 15:03 reaches 1M Speed | Positive, 16:09 | 

 ElAbrahaham | ES | 20 Aug | 2.6M | Heavy | "185,000 players" | 3:15 hit by a player · 5:51 "boring" after seeing maxed pets · 6:02 2,000 R$ on eggs · 8:42 buys a billion Speed · 13:36 "not really understanding" | "Nothing more to do", 18:29 | 

 Paulberger | DE | 20 Aug | 205K | Yes | "260,000 players" | 1:12 "TikTok! Crazy" · 2:31 bat harassment · 3:38 buys ×2–×32 Speed · 8:57 instant hatch "so expensive" · 10:06 recommended numbers "aren't right" | — | 

 Foltyn | EN | 20 Aug | 4.9M | Heavy | "150,000 playing" | Trapped by a chicken and a player on run 1 · golden chicken · scrolling Reels: "going to rot everybody's brains" | Strawberry elephant | 

 Kaydee | FIL | 21 Aug | 150K | 3 R$ | — | 1:26 first steal · 3:04 hunts for the treadmill · 3:35 annoyed by TikTok, chat says turn it off · 7:47 trapped · 13:17 buys ×2 Speed for 3 R$ | Good mood, 17:08 | 

 NonSytic | VI | 21 Aug | 155K | No | "Hot on international Roblox" | 0:25 goose knocks the egg away · 1:01 "Why is there TikTok?" · 1:41–5:41 strangers gift eggs · 3:15 trapped ("so evil") | ~10 min | 

 AMAAN-T | HI | 22 Aug | 56K | No | — | 1:22 caught, "need the treadmill" · 4:05 learns eggs can't be sold · 5:08 can't find treadmill upgrades · 11:52 wishes there was rebirth | 15:47 | 

 KreekCraft | EN | 22 Aug | 110K | Heavy | Trend, chat | 2:53–7:14 Reels riff · 13:40 annoyed at dragging eggs to the hotbar · 20:44 bat-hit repeatedly during Admin Abuse | "Never playing this game again" | 

 LvBlox | EN | 23 Aug | 18 | No | "New popular game" | 0:55 first egg · 1:42–7:00 Reels, quits after an inappropriate clip · 8:50 10K Speed · 17:14 too slow for a Secret egg · 26:26 finds treadmill upgrades · 35:11 eggs stolen | "Pretty good start", 36:00 | 

 Tincho88 | ES | 27 Aug | 4.5K | No | — | 0:52 caught by the chicken · 2:06 treadmill appears · 4:44 "I can only have seven" · 8:34 "kind of lost with the game" | 12:02 | 

 Noki | TR | 31 Aug | 85 | No | "About 2M players" | 0:38 reads Speed gates · 1:46 "made for new players?" · 6:37 "enjoyable, at first it looked boring" · 8:03 can't escape the tiger | 12:32+ | 

 SkellyPlays | EN | 2 Sep | — | No | — | ~20 min: 4th biome · ~30 min: 8h Eternal egg · ~1.5 h: 3.3M Speed · ~2.5 h: 22M and first Secret | Post-Reels build | 

Why new players stay or quit

 Ranked from about 1,900 top YouTube comments on 30 videos (7 Aug – 10 Sep) and fan-wiki FAQs. Commenters read as mostly 8–12 years old.

Rare-egg lottery at every reset20+"the night restock just makes it addictive like OH YEAH 1 MORE"

 Big numbers that are easy to brag about25+"by the second day I got to 11.6 bil"

 Stealing, when you're the thief~10"satisfying to get a good egg that nobody else gets"

 Low effort and AFK-friendly~12"The doomscrolling feature was goated, Why was it removed"

 Constant new contentsome

 Strangers gifting eggsfew

Exploiters and bots take rare eggs30+"every server I joined consisted of 2 exploiters at the minimum" Kids can't tell Robux buyers from cheaters.

 No private or beginner servers20+"I suck and no private servers so I have to quit"

 Pay-to-win, a hopeless grind without Robux20+"its js to grindy if you don't spend robux"

 Egg stolen at the last second~10"a 10 hours to hatch [egg] and a noob got it"

 Takedown confusion and refund worriesburst

 Repetitive "brainrot slop"older players

 Mobile lagsome

Most common beginner questions:

How do I get Speed, and why am I stuck?

 Why does the guardian catch me when I have the recommended Speed? (Big eggs are heavier, and the game never says so.)

 How do I get a private or empty server?

 What should I spend money on first?

 Is that player hacking or paying?

 Basic PC controls: how to open the inventory, how to steal.

 Was the game deleted, and did I lose my progress? (24–25 Aug)

Other angles

Mobile
The carrying state works well on a phone: RUN!! banner, Drop button, arrow trail. The problems are side-by-side cash and Robux buttons (an accidental Robux purchase was caught on camera), other players' pets blocking taps, traps that can't be seen from the default camera angle, and a bat swung by tapping anywhere that nobody discovers. No early mobile footage was found, and no tracker publishes a phone vs. PC split.

Short-form video
The most-viewed clips were comedy skits set in the game (15–21M), followed by giant and rare egg reveals (1.4–9.7M) and "waited 3 days" hatch payoffs. Griefing and $/s numbers never headlined a clip. The developer's TikTok ("Ben") started around 9 Sep, after the peak.

Clone wave
47+ copies from four Roblox searches, peaking at 18 in the week of 4–10 Sep. Together they hold about 208K players, roughly 22% of the original. The ones that kept players added a mechanic: Break and Steal an Egg (78K), Drill, Clone, Wings, Swing. Theme-only copies (Anime, Brainrot) stayed small.

Regional
The first two minutes play out the same way in every language: chicken, chase, golden "Mama" pet. Spanish and German creators spent Robux heavily, while Portuguese, Vietnamese, Tagalog and Turkish creators mostly didn't. Filipino and Indonesian players focused on recognizing brainrot characters.

05 · Player lifecycle

 How a typical player's time with the game unfolds

 The long view: six stages, from the first click to week-two veterans. Speed gates are guide-reported recommendations, not hard locks, and the developer has shifted them between patches.

0–2 minFirst steal

The player spawns with 10 Speed and $0. A banner reads "Steal an Egg!", a chicken snores next to a nest in the Forest, and an arrow trail leads to it. The first egg hatches instantly, and the tutorial says "Place your Pet!" next to a sign that reads "Unlock: $1K" (the treadmill).

Keeps them playing

 Under 30 seconds to the first reward, with no menus in the way.

 A sleeping guardian is readable and funny: the snoring, the "!!" when it wakes, the "RUN!!" banner.

 The first hatch is rigged to feel lucky (a golden chicken).

 Other players' giant pets and dragons are visible across the map, which sets an aspiration immediately.

Might make them leave

 Getting trapped or bat-hit by a veteran on the very first run. Foltyn's first session opened with exactly this ("If I keep getting hit, I'm going to go into a private server").

 The first pet earns about $2/sec, which reads as meaningless next to other players' billions.

 Night arrives mid-run and wipes the field without warning.

Spawn state: 10 Speed, $0, Shop and Index buttons, and a day/night timer at the bottom right. A sleeping chicken guards the nest. Frame from Foltyn's first session.

2–15 minForest → Lake, unlock treadmill

The player loops Forest and Lake eggs (Lake recommends about 900 Speed), fills a small pen, and saves up $1,000 to unlock the treadmill. A fresh-account YouTuber estimated "like 10 minutes" of waiting at $2/sec before the treadmill was affordable. Claiming the free 10K Speed (like + favorite + join group) and the first Index rewards (Lake pet: +3,500 Speed, +$30K) is what actually gets most people over this hump. This is also the stage where the average session (~15 minutes) ends.

Keeps them playing

 The reset clock: "in 2m 30s" on screen always gives a reason to wait for the next batch.

 Night growth (about 30×) turns waiting into a rush to drop eggs before dark.

 Index rewards make every new species pay out instantly.

 A 3-Robux "x2 Speed" offer is the cheapest possible first purchase.

Might make them leave

 Without the free Speed or Robux, the treadmill unlock is a slow, flat wait.

 The pen fills and the player has to learn Sell and Equip Best with no explanation.

 Repeated PvP griefing near the spawn biomes.

15–60 minBiome ladder

The treadmill turns Speed into a number that visibly climbs every second. The player moves up one biome at a time: Desert (10K), Jungle (40K), Snow (170K), Volcano (700K). Each biome brings a new guardian (scorpion, tiger, yeti, Cerberus), a new set of eight pets, and longer hatch times. Trails (10 tiers, Common to Divine) multiply Speed gain. YouTubers who paid for treadmills reached Snow in under 10 minutes; free players take much longer.

Keeps them playing

 New biome equals a new creature to outrun. The visual payoff is big for each gate cleared.

 "Almost there": recommended Speed is shown, so the next goal is always one number.

 Pets jump an order of magnitude per biome (owl $38/s → mammoth about $1M/s).

 Long hatch timers (15 min and up) start creating reasons to come back.

Might make them leave

 Upgrade costs outpace income (one restart saw a treadmill upgrade cost $120M while holding $7K).

 Pay-to-win is visible. A "bacon hair" with a 1,750-Robux treadmill outruns everyone.

 Big eggs slow the carrier, so a single misjudged run loses the egg.

The chase: a "RUN!!" banner when the guardian wakes, and a Drop button while carrying. Other players' oversized pets show on the horizon. Frame from Foltyn.

Day 1–3Rare hunting, multi-session

The game turns into an appointment game. Players leave Secret and Eternal eggs hatching (1h30m to 7h), AFK on the treadmill, and come back. Rare eggs (Secret, Eternal, Divine) can spawn in any biome at reset and the whole server is notified in chat, which sets off a 7-player scramble. Every server rolls a different size for the same egg, so players hop servers for bigger ones. Fusing three duplicates and applying mutations (Silver 1.25× up to Rainbow 3.5×, depending on source) become the main income levers.

Keeps them playing

 Server-wide rare announcements create shared, spiky excitement.

 Hatch timers are natural "come back later" hooks, with an instant-hatch upsell.

 Size and mutation variance make two copies of the same pet worth very different amounts, so duplicates still matter.

 The Index works as a checklist with real rewards (Eternal Index: +2M Speed, +$25B).

Might make them leave

 Rare eggs are single-claim. One faster player or one exploiter with auto-collect takes it from everyone else.

 Exploit scripts ("Auto Steal", "Auto Hatch") are widely advertised, and banned players come back on alt accounts.

 Crowded 7-player servers camping the Cosmic biome.

Week 1–2Live-ops regular

The player now lives on the weekly schedule. Saturday brings Admin Abuse: the developer joins servers, stacks luck, money, growth and Speed multipliers, spawns free admin treadmills and rare eggs, and then ships a new update right after. Updates add a biome with higher gates (Cherry Blossom ~2.5B, Titan Temple ~7B, Angels & Demons 20B night-only, Enchanted Forest 50B), a limited premium egg, and an event with its own currency: Dr. Scramble boss fights every 30 minutes with 22 mastery milestones, Nitro Races every 30 minutes for trophies, star-fed mounts.

Keeps them playing

 A fixed weekly ritual: players are told to join 15–20 minutes early because servers fill.

 New pets keep appearing above the current top, so the ceiling keeps moving.

 Co-op moments such as boss fights and server-wide story events (the "Sammy" crossover with Steal a Brainrot).

 Mutation stacking ("Silver + Fractured + Scrambled") creates bragging rights.

Might make them leave

 Each update gates new content at billions of Speed, which punishes newcomers and lapsed players.

 Power creep makes last week's best pet obsolete.

 Event shops are time-limited (the racing event ran for one week), which pushes FOMO but also burnout.

 Cheating reports sent through Discord often go unresolved.

Admin Abuse: a "Capture the Egg" hold contest at the top, five stacked boost timers (×2 to ×6) at the bottom right, and the night countdown. Frame from "I Spent 100 Days in Steal an Egg".

EndgameFlex and min-max

Top players sit on hundreds of billions of Speed and quadrillions of cash. Their play is chasing Divines (12–20h hatches), stacking mutations on giant eggs before opening them, buying limited Robux eggs for 1% chase pets, and showing off. The "#1 player" videos pull over a million views each, so the endgame works partly as marketing.

Keeps them playing

 Status: their pets are physically on display in the base for everyone in the server to see.

 "World's first" hatches of each new Divine.

 Trading and gifting (with scam risk), plus Steal DNA copies of other players' pets.

Might make them leave

 Nothing left to unlock between updates.

 Income numbers become too large to mean anything.

 The best items increasingly route through Robux.

Late game: a triple-mutated Eternal earning $4.1B/s, with the Sell pad and Fuse Machine in the base. The HUD reads 310.2B Speed and $928T. Frame from "100 Days".

06 · Mechanics

 Mechanics table

 For each system: how Steal an Egg does it, what makes it work, and the common ways clones and similar games get it wrong. Scroll the table sideways on narrow screens.

Mechanic | How it works in Steal an Egg | Details that make it good | Common mistakes | Screenshot | 

Guardian steal runCore action | 
 Each biome nest has a sleeping guardian creature. Grabbing an egg wakes it. If it catches you, you ragdoll and drop the egg. Caught inside its biome, the egg returns to the nest. Caught after crossing into an earlier biome, the egg stays on the ground for a second try. | 
 Clear pass/fail in 10–40 seconds.
Telegraphing: snoring, "!!", a "RUN!!" banner.
The partial-progress drop rule softens failure.
Creature personality (chicken → T-Rex → cosmic dragon) is the progression art.

 | 
 Guardians that rely on hidden stat checks, so players can't tell if a failure was skill or numbers.
An instant-loss catch with no recovery.
Identical guardian behavior in every biome.

 | 
 | 

Speed stat and biome gatesMain progression axis | 
 One number. Recommended gates: Lake 900, Desert 10K, Jungle 40K, Snow 170K, Volcano 700K, Abyss 2.5M, Prehistoric ~17M, Cosmic 700M, Cherry Blossom ~2.5B, Titan Temple ~7B, Angels & Demons 20B, Enchanted Forest 50B. | 
 Extremely legible: one stat, one goal.
Gates are soft. A skilled player can sneak a small egg out of a higher biome early, which rewards skill.
The stat is also your PvP safety.

 | 
 Exponential gates without matching income growth, which produces the mid-game wall.
Hard locks that remove the "can I sneak one?" gamble.
Raising gates every update, which strands newcomers.

 | 
 | 

TreadmillIdle Speed source | 
 Unlocks for $1,000 in the base. Standing on it adds Speed per second, works while AFK, and is upgraded with cash. Nine cosmetic/power tiers sell for Robux (19 → 1,299), plus Astral (1,560) and Guardian (1,750). | 
 Turns idle time into visible progress.
A physical object in the base that others can see and envy.
A cheap first tier (19 R$) gets players used to paying.

 | 
 Robux tiers so strong that free play feels pointless. YouTubers openly call it "pay to win."
No reason to leave the treadmill, so players stop playing the actual game.

 | 
 | 

Reels feed on the treadmillRemoved Aug 2026 do not copy | 
 A TikTok-style vertical video screen with likes, comments and shares, mostly AI-generated or uncredited clips. Players scrolled while gaining Speed. A Settings toggle could turn it off, but few players found it. The game was offline from 22:21 UTC on 24 Aug to 01:44 UTC on 25 Aug and returned without Reels. Reports of a compensation reward conflict. | 
 It filled the dead time on the treadmill, which shows the real design problem: idle phases need something to do.
It created viral controversy (a 4.8M-view first-impressions video).

 | 
 Now against Roblox policy for Kids/Select catalogs: a media feed plus autoplay or infinite scroll plus rewards tied to watching.
Copyright exposure from unlicensed clips.
Fill idle time with game actions instead.

 | 
 | 

Pen and hatch timers | 
 Eggs must be placed in your pen to grow. Times scale with rarity: Common 10–20s, Epic 1–3m, Cosmic 8–33m, Secret 1.5–3h, Eternal 4–11h, Divine 12–20h. Night speeds growth about 30×. Size and mutations change the timer. A 2× growth pass costs 467 R$, and instant hatch costs 9–299 R$. | 
 Short early timers keep the first session snappy.
Long late timers create return visits.
The anticipation of a big egg sitting in the base is an advert for everyone else in the server.

 | 
 Long timers in the first hour.
Pens too small to hold the eggs a player wants to stockpile.
Timers that only end in a paywall.

 | 
 | 

Passive income and pen slots | 
 Each placed pet earns $/sec based on species, size and mutation. Pen upgrades add slots (the HUD shows "1/9 Active"). Equip Best auto-sorts. Weak pets go to the Sell pad. | 
 "A full pen of commons beats a half-empty pen with one rare," which rewards activity over luck early.
Equip Best removes busywork.
Size makes duplicates still worth something.

 | 
 Slot caps so tight that every hatch needs a sell decision.
Number inflation (B, T, Qa…) so fast that values stop meaning anything.

 | 
 | 

Rarity ladder | 
 Ten tiers: Common, Uncommon, Rare, Epic, Legendary, Mythic, Cosmic, Secret, Eternal, Divine. About 8 pets per biome, 100+ in total, with brainrot-meme pets mixed in (Brr Brr Patapim, Tralalero). | 
 Every biome contains the full low-to-high spread, so lower biomes stay relevant.
Recognizable meme pets help discovery on TikTok and YouTube.

 | 
 Too many tiers with unclear color coding (players say "it's purple, I can't remember what rarity").
Rarity that doesn't line up with income.

 | 
 | 

Mutations | 
 Multipliers on income, reported as Silver 1.25×, Bloom 1.25–1.5×, Golden 2×, Rainbow 2.5–3.5×, Spirit Bloom 2.5–3×, plus later Fractured, Parasite, Scrambled and Nitro. They come from spawns, the Sakura Incubator, event crystals or bottles applied to eggs (which can "fizzle"), and race shops. Multiple mutations stack. | 
 Adds a second roll on top of species.
Applying mutations to eggs before hatching gives players an active role in their own luck.
Stacked names read as trophies.

 | 
 Undisclosed odds (guides disagree on every value).
No visual way to tell whether an egg is already mutated (the 100-days player kept asking).
Each update adds a stronger mutation, which devalues old ones.

 | 
 | 

Egg reset and day/night cycle | 
 About every 5 minutes the map fills with a new random batch. Night (around 10–13 seconds) closes the field, then a new day starts. Some biomes are night-only. | 
 A visible countdown creates micro-goals ("beat the night").
Everyone in the server competes for the same fresh batch.

 | 
 Resets that wipe eggs mid-carry with no warning.
Cycles that are too long, leaving dead air.

 | 
 | 

Rare spawn announcements | 
 Secret, Eternal and Divine eggs spawn at random at a reset in any biome, including Forest. A server-wide message goes out. Each egg can be claimed once. Each server rolls its own size. | 
 Spiky, shared excitement that pulls low-level players into the action.
Big moments for streams and clips.

 | 
 Single-claim spawns plus weak anti-cheat means exploiters win every time.
Only the fastest player can ever win, so newcomers learn to ignore the alert.

 | 
 | 

PvP interceptionBats and bear traps | 
 A bat ragdolls and knocks back an egg carrier. A bear trap roots anyone, including guardians, for 5 seconds and makes them drop their egg. Traps are limited and granted through Index rewards. Limited bats come from chests and events. | 
 Social conflict is what gives the game staying power.
Traps work on guardians too, so defense and offense share one tool.
A weaker player can still steal from a stronger one.

 | 
 No protection for new players, so veterans grief the Forest.
Trap minefields around spawn (seen during events).
No consequence for repeat griefing.

 | 
 | 

Capture the Egg | 
 An event minigame where players fight over one giant egg. The holder earns time (around 30 seconds to win), and others bat them to take it. | 
 King-of-the-hill gives PvP a clear goal.
Comebacks are possible.
Losers quit early, so rounds stay short.

 | 
 The fastest player wins every round. Needs Speed normalization.
Cutscenes that kill players mid-hold (reported in the 100-days run).

 | 
 | 

Index (collection book) | 
 Each first discovery of a species can be claimed for Speed plus cash. Rewards scale hard: Lake pet +3,500 Speed and $30K, Eternal +2M Speed and $25B. One restart got 300K Speed from a single claim. | 
 Pays players to explore the full roster.
Works as a catch-up boost at every stage.
A red badge on the button drives the habit of checking.

 | 
 Rewards too small to notice.
Collection gated behind Robux-only eggs.

 | 
 | 

Fuse Machine | 
 Three identical pets become one pet of the next rarity, earning more than the three combined. Event shrines fuse specific pairs (Pegasus + Skeleton Horse → Equinox). | 
 Duplicates become progress, not trash.
Gives a deterministic path alongside random luck.

 | 
 Fusion that destroys size or mutations without warning.
Recipes that aren't shown in-game.

 | 
 | 

Trails | 
 Ten trails from Green to Divine. Each multiplies Speed (Green 2×, Divine ~14×) and leaves a visible trail. Bought with cash, or with Robux from 19 to 999 R$. | 
 A cosmetic that is also power, visible on every run.
A clear "next purchase" target.

 | 
 Unclear stacking. A YouTuber bought Divine and felt slow because the multiplier applies to treadmill gain, not current Speed.

 | 
 | 

Admin AbuseWeekly live event | 
 Every Saturday (around 11 AM ET), 30–60 minutes. The developer stacks global boosts (luck, growth, money, Speed), hands out admin treadmills, spawns rare eggs and runs minigames (Dragon Lair, Monster Obby, story crossovers), then releases the week's update. | 
 An appointment ritual with a fixed time, shared across all servers.
The developer appears as a character.
Bundling it with the update means the biggest crowd sees new content first.

 | 
 Seven-player servers fill and players get locked out.
Unclear timing across time zones.
Boosts so large that normal play feels flat afterwards.

 | 
 | 

Timed in-update eventsBosses, races, mounts | 
 Dr. Scramble boss portal every 30 minutes (13,300 HP, 3 phases, 22 mastery milestones, damage-scaled "Samples" currency, a lab gacha with a 100-point pity bar). Nitro Race every 30 minutes (2,500 trophies per win, trophy shop, Win Pass with 5 pets). Mounts fed with stars that drop at night. | 
 A 30-minute cadence fits inside a session.
Pity bars and mastery tracks guarantee progress.
Co-op bosses balance out the PvP.

 | 
 Week-long shops create FOMO burnout.
"Must be in the server since the start of day" rules for star drops punish late joiners.
Every event adds a new currency.

 | 
 | 

Server size and hopping | 
 Seven players per server, private servers disabled. Players hop to find a lobby where they are the fastest, or one where a rare egg rolled bigger. | 
 Small servers make every player and every base visible.
Competition stays personal.

 | 
 No private servers means no refuge from griefers.
Hopping becomes the optimal strategy, which hollows out play in each server.

 | 
 | 

07 · Monetization

 What is for sale

 From the game's public product list on 11 Oct 2026: two game passes and 109 developer products. The structure is an escalating ladder that starts at a trivial price and climbs into thousands of Robux.

Category | Items and prices (Robux) | Design note | 

Game passes | X2 Money 399 · X2 Growth Speed 467 | Only two permanent passes. Most revenue comes from repeatable products. | 

 Speed multiplier ladder | Tier 1: 3 · T2: 9 · T3: 24 · T4: 39 · T5: 79 · T6: 129 · T7: 199 · T8: 299 · T9: 499 · T10: 649 · T11: 949 · T12: 1,375 | A 3-Robux first step designed to turn a player into a buyer. It is advertised on the HUD as "x2 Speed ONLY 3 R$". | 

 Speed packs | +150K 79 · +1M 249 · +10M 699 · +50M 1,499 · +500M 1,999 · +1B 2,999 | Skips biome gates outright. YouTubers buy +1B and go straight to Cosmic. | 

 Treadmills | Sci-Fi 19 → Flame 39 → Celebrity 69 → Golden 119 → Freeze 199 → Lucky Block 299 → Hacker 499 → Demonic 799 → Angelic 1,299 · Astral 1,560 · Guardian 1,750 | New top treadmills ship with updates and reset the ceiling. | 

 Trails | Green 19 → Blue 29 → Purple 49 → Golden 79 → Red 119 → Galaxy 199 → Secret 349 → Eternal 599 → Divine 999 · Moonbloom 1,199 | Also purchasable with in-game cash. | 

 Instant hatch | 9 (≤15 min left) → 299 (≤12 h left) · Grow All 199 | Priced by remaining time, so the long timers are the sales pitch. | 

 Limited eggs | x1 99 · x3 249 · x10 799 · x50 3,499 (Brainrot, Extinction, Monster, Luminous) | A rotating featured egg with 0.5–1% chase pets. | 

 Steal DNA | Common 49 → Divine 4,999 | Creates an exact egg copy of another player's pet. It monetizes envy directly. | 

 Boosts | Speed 5 min 90 → 4 h 499 · Luck 49/99/199 · 2× Earnings 99 · Mount Stars 1/3/5 for 399/899/1,399 · 2× Trophies 299 | Timed boosts line up with events. | 

 Rewarded ads | [AD] 2×/4× cash for 30 s · [AD] 2× egg growth 5 min | Roblox rewarded video ads, a free path for players who don't pay. | 

08 · Live ops

 Update history

 The developer shipped roughly one update a week after launch. Dates come from guide sites and the developer's event listings. Some intermediate events have no confirmed date.

Date (2026) | Event | What it added | 

25 Jul | Launch | Core loop, treadmill with Reels, trails, Fuse Machine, Index. By mid-August guides list nine biomes (Forest → Cosmic). | 

 3 Aug | Game passes | X2 Money and X2 Growth Speed created. | 

 7–13 Aug | Charts | Up-and-Coming #1 (7 Aug), then Top Trending #1 and Most Popular (13 Aug). See How new players arrive. | 

 22 Aug | Admin Abuse #1 + Update 1 | Cherry Blossom biome, Sakura Incubator (unlocked by hatching a Crane), Bloom and Spirit Bloom mutations, Dragon Lair minigame. | 

 ~25–26 Aug | Takedown | Offline from 22:21 UTC on 24 Aug to 01:44 UTC on 25 Aug (about 3.5 hours). It returned without the Reels feed. Roblox then announced its media-feed policy (DevForum, 26–28 Aug). Reports of compensation conflict. | 

 29 Aug | Monster Update | Titan Temple (~7B), Hungry Monster event, Monster Chests, Monster Obby, Monster limited egg. | 

 ~5 Sep | Rift event | Rift bosses about every 30 min, Rift eggs and trade-in machine (date from one guide). | 

 12 Sep | Update 4: Angels vs Demons | Night-only Angels & Demons biome (20B), shrine fusions, Luminous limited egg (one source). | 

 19 Sep | Update 5: Sammy crossover, Dr. Scramble Part 1 · all-time peak | Steal a Brainrot's Sammy visits in a live story event, then Dr. Scramble drones and the Experiment shop. 14.29M concurrent players that Saturday. | 

 26 Sep | Update 6: Dr. Scramble's Revenge | Boss every 30 min, Boss Mastery (22 milestones), Dr. Scramble's Lab gacha with pity, Extinction egg. | 

 Early Oct | Enchanted Forest | 50B gate, Royal Skywhale (20h Divine). | 

 10 Oct | Update 8: Mounts + Nitro Race | Ride pets using stars, races every 30 min, trophy shop, Win Pass, Nitro mutation. | 

09 · Reference images

 Screenshots

First seconds: Forest, sleeping chicken, 10 Speed, $0.
 Tutorial: first hatch, "Place your Pet!", treadmill unlock sign for $1K.
 Escape: "RUN!!" banner and Drop button.
 The removed Reels feed, with a "+200 Speed" ticker and a 3 R$ speed offer.
 Admin Abuse: Capture the Egg and stacked boost timers.
 Late game: stacked mutations, Sell pad, Fuse Machine.
 Official store art: T-Rex guardian (Prehistoric).
 Official store art: the "YOU!" framing puts the player in the role of the thief.

Gameplay frames are stills from Foltyn, "ROBLOX STEAL AN EGG.." and "I Spent 100 Days in STEAL AN EGG!", used here for reference. Promo art is from the game's Roblox store page.

10 · For your build

 Lessons for a similar game

Get to the first chase in 30 seconds
Spawn facing a sleeping guardian with eggs beside it. No menus, no dialog. Make the first hatch instant and slightly lucky. Across 15 early videos in eight languages, the chicken, the chase and the lucky first pet opened every session. This is the window Roblox scores as under-60-second and 61–180-second bounce.

Give the guardian a personality
The guardian is the content. A distinct creature, sound and chase behavior per biome does more work than any stat table.

Make the first steal PvE only
Being bat-hit or trapped in the first 2–3 minutes shows up in videos in four languages, and "no private servers" is the #2 quit reason in comments. Use a grace period, a starter biome where PvP tools don't work, or beginner lobbies that players asked for outright. Bring in PvP once the player owns something worth defending.

Get through the 20–40 minute wall
Upgrade costs jump to $5M and then $3B while income lags, and players don't find treadmill upgrades until minute 26. Show the next upgrade when it becomes affordable, make sure each gate can be reached within one session, and point to the right next purchase.

Explain the hidden rules
Egg weight slows the carrier, traps don't stop guardians, eggs can't be sold, fusing needs 3 identical pets, and night resets everything. Each one generates questions and false "hacker" accusations. Show weight on the egg and add one line of UI per rule.

Show who paid
Kids can't tell Robux buyers from exploiters, so every fast player looks like a cheater. Visible paid-boost auras or badges turn that into aspiration, and server-side pickup validation removes the real cheaters.

Separate Robux from cash on mobile
Side-by-side cash and Robux price buttons led to an accidental Robux purchase on camera. Give each a different shape and color, use large touch targets, and add a confirm step for Robux. Hide other players' pets by default on phones.

Add day-2 and day-8 reasons to return
Steal an Egg has no daily login rewards, streaks, quests or codes, and leans entirely on timers and Saturday events. Roblox now measures retention out to day 28, and that's where this game is weakening.

Fill idle time with play
The treadmill shows that players need something during idle phases. Use minigames, base decorating, short chase events or pet interactions. A video feed with rewards breaks Roblox policy for younger players.

Use catch-up sources that scale
The Index is the best free catch-up tool in the game: first discoveries pay out Speed in proportion to the stage. Build the same into every new biome so returning players aren't stranded.

Make rare spawns fair
Single-claim eggs plus auto-collect exploits is the top community complaint. Validate pickups on the server (distance, rate, line of sight), and consider per-player instanced rare eggs or a shared-credit rule.

Put live ops on a fixed weekly time
A Saturday event with the developer present, followed straight away by the update, gave the game a weekly ritual. Plan server capacity for that spike.

Keep the paid ladder from replacing play
A 3-Robux first purchase is effective, but treadmill and Speed packs that skip whole biomes empty out the core loop. Sell cosmetics, convenience and timers more than raw progression.

Make outcomes legible
Show whether an egg is mutated, what each trail multiplies, and what the odds are. Guides disagree on nearly every number in this game, which tells you players couldn't find the answers in-game.

Unverified: one third-party calculator describes a Rebirth system (base cost about $250K scaling 3.5× per tier, resets cash and Speed, +50% cash and treadmill gain per rebirth). No guide or official source confirmed it, so it is left out of the timeline. Base defenses (walls, alarms, turrets) on one fan wiki were also unconfirmed and are omitted.

11 · Sources

 Sources

Roblox public APIs (games, votes, game passes, developer products, media), queried 11 Oct 2026, universe 10563114921

 Steal An Egg on Roblox

 Kotaku: Roblox cracks down on doomscrolling games

 GamesRadar: Roblox takes down doomscrolling game

 Dexerto: treadmill scroll

 Player.One: player counts and session length

 RoWatcher stats

 AllThings.How beginner guide

 TechWiser beginner guide

 Games.gg beginner guide

 Bloxodes beginner guide

 Bloxodes Update 6 guide

 Bloxron stealing guide

 Eldorado: Admin Abuse schedule

 Eldorado: eggs list and hatch times

 Beebom: mutations

 AllThings.How: first Admin Abuse

 Fan wiki: treadmill

 TechWiser: racing event

 Sportskeeda: Update 8 patch notes

 Games.gg: Dr. Scramble

 LOLGA: cheating and private servers

 YouTube: Foltyn first session

 YouTube: 100 Days in Steal an Egg

 YouTube: Starting over with Guardian treadmill

 Rotrends: chart placements and history

 Rolimon's: daily concurrency and session length

 Roblox Creator Hub: discovery and recommendations

 DevForum: media-feed restrictions

 Misfits Gaming on X: #1 game, 371K CCU

 TheStreet: engagement cooling (Bank of America)

 AllThings.How: takedown timeline

 Fan wiki FAQ (beginner questions)

 Early first-time videos listed in section 04, plus about 1,900 comments from 30 early videos

 Short-form clips: Vindooly skit, gorilla egg reveal

 Mobile: SenpaiUnlimited and Mangu Pehlwaan first-time mobile videos (24 Sep, 9–10 Oct)

 Wikipedia: Steal a Brainrot, Grow a Garden
```
