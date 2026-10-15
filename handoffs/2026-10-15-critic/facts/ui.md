# UI facts: StarterGui + what a brand-new player sees at spawn

Method: rects below are NOT hand arithmetic; each viewport was reproduced by cloning the Hud elements into a temporary fixed-size Frame (667x375, 844x390, 1920x1080) inside a throw-away ScreenGui under StarterGui (deleted right after; verified gone), applying HudController's Compact* attributes where the controller would, and reading AbsolutePosition/AbsoluteSize from the engine. Text px = largest size that fits the label box (TextService:GetTextBoundsAsync; matches the engine's own scaled TextBounds). Origin (0,0) = top-left of the Hud ScreenGui.

Assumptions: (1) Device Simulator unavailable, so no notch/cutout insets (real phones with a notch lose ~44-47 px each side horizontally via ScreenInsets=DeviceSafeInsets, which shifts Left/Tabs/Stats inward but doesn't resize them). (2) Hud has IgnoreGuiInset=true, so y=0 is the physical top; the Roblox topbar occupies roughly y 0-58 (GuiService inset is 58 in the new topbar; HudController uses it: top stack starts at inset+6 = 64). (3) Touch/short-screen rule (HudController.isCompact): COMPACT profile when UserInputService.TouchEnabled-only OR Hud height < 600; so 667x375 and 844x390 = COMPACT, 1920x1080 non-touch = BASE. (4) Roblox default mobile controls: thumbstick appears in the left ~half, jump button bottom-right (about 70x70 at roughly x W-95..W-25, y H-90..H-20 on small screens); thumb zones are the brief's: joystick x<200,y>H-200; jump/action x>W-200,y>H-200. (5) Tutorial step 1 text from Config.Tutorial: title "Steal an egg!", body "Sneak up to the Meadow nest and hold to steal an egg.", counter "1/16" (Studio default shows "1/7"; there are 16 steps).

## ScreenGuis in StarterGui
| ScreenGui | Enabled | DisplayOrder | IgnoreGuiInset | ResetOnSpawn | ScreenInsets | Notes |
|---|---|---|---|---|---|---|
| Hud | true | 1 | true | false | DeviceSafeInsets | main HUD, 19 children, 290 descendants |
| Menus | true | 5 | true | false | DeviceSafeInsets | 13 children; every modal is Visible=false; only `ShopFx` (empty transparent full-screen overlay, Z60, Active=false) is Visible |
| Admin | true | 10 | false | false | CoreUISafeInsets | `Toggle` and `Panel` both Visible=false (AdminController shows Toggle for admins only) |
| Events | true | 3 | false | false | CoreUISafeInsets | `Banners` frame Visible=true but 0 px high and empty (NightRun/Capture/Boss/Rare banner templates are Visible=false until an event) |
(HudController also creates runtime ScreenGuis, e.g. "HudReveal" and a HintPill inside Hud; none visible at spawn.)

## Elements visible at spawn (new player, TutorialStep 1) - base definition in StarterGui
Visible-by-default in StarterGui and NOT hidden by HudController/TutorialController at join:
| Element | Class | Size | Position | Anchor | Constraints | COMPACT overrides (phones) |
|---|---|---|---|---|---|---|
| Hud.Stats | CanvasGroup | {0.2,0},{0.15,0} | {0,12},{1,-12} | (0,1) | UISizeConstraint min 210x112, max 320x128; UIListLayout; children Speed(Frame .82x.38) and Money(Frame 1x.56) with icons (UIAspect 1) + TextScaled labels (Value, Income) | Pos {0,8},{1,-8}; Size {0,194},{0,96}; USC min=max 194x96 |
| Hud.Left | CanvasGroup | {0.085,0},{0.3,0} | {0,12},{0.46,0} | (0,0.5) | UIAspectRatio 0.504 (FitWithinMaxSize, Width); USC min 120x238; UIListLayout pad 10; buttons Shop, Daily, Quests, Rebirth each {0.95,-6},{0.2,0} + UIScale | Pos {0,8},{0,64}, anchor (0,0); Size {0,86},{0,172}; Aspect 0.5; USC min 86x172 |
| Hud.Tabs | CanvasGroup | {0.053,0},{0.44,0} | {1,-12},{0.56,0} | (1,0.5) | USC min 60x150; UIListLayout pad 12; buttons Eggs, Pets, Index each {0.95,-6},{1,0} | Pos {1,-8},{0,108}, anchor (1,0); Size {0,54},{0,186}; USC min 50x150 |
| Hud.Timer ("NEW EGGS IN 5:00") | Frame | {0.139,0},{0.0762,0} | {1,-14},{1,-14} | (1,1) | UIAspect 3.71 (FitWithinMaxSize); USC min 160x43 | Pos {1,-8},{0,64}, anchor (1,0); Size {0,140},{0,38}; Aspect 3.68; USC 132x36..140x38 |
| Hud.Settings | TextButton | {0,110},{0,44} | {0,12},{1,-190} | (0,1) | UIScale 1, UIPadding | Pos {1,-156},{0,64}, anchor (1,0); Size {0,80},{0,34} |
| Hud.Tutorial (step card, Z40) | CanvasGroup | {0.5,0},{0.1,0} | HudController places at {0.5,0},{0,64} (top-centre stack) | (0.5,0) | UIAspect 7 (FitWithinMaxSize); USC min 400x64 max 900x130; UIScale | Size {0.4,0},{0.1,0}; Aspect 6; USC min 330x56 |
| Hud.FirstWalk.Distance pill ("Meadow nest: N studs", step 1 only) | TextLabel in full-screen FirstWalk Frame (Z41) | {0,0},{0,30}, AutomaticSize X | centred under the card: x = card centre, y = card bottom + 4 | (0.5,0) | TextSize 20 (not scaled) | none |
Present but empty/invisible at spawn (Visible=true, no visible children): Hud.Boosts (Frame 0.3W x 34 at {0,12},{1,-148}; compact {0,8},{1,-112} 236x34; boost chips are added at runtime), Hud.Toasts (list; toasts appear on events), Hud.FxLayer, Hud.FirstWalk (container), Events.Banners, Menus.ShopFx.
Hidden at spawn (Visible=false): Carry (egg carry card), RunBanner, RunBanner2, LockBanner, RunRed, FriendBoost, Shield, Spotlight (only for HUD-highlight tutorial steps 8-9), FirstWalk.Banner/Streaks (one-time corridor whoosh), all Menus.* modals, Admin.*. Badges (Daily/Quests/Rebirth/Eggs/Index) Visible=false until there is something to claim.
Nothing is auto-opened at join (no OpenModal call on Start in any controller); no controller hides Shop/Daily/Quests/Rebirth for new players, so the full side-bar is always on even though only step 1 is relevant.
Roblox core UI is untouched (no SetCoreGuiEnabled anywhere): topbar icons, chat, player list, backpack hotbar (when tools exist) and the mobile joystick/jump all show.

## Always-on HUD element count
- Persistent buttons: 8 (Shop, Daily, Quests, Rebirth, Eggs, Pets, Index, Settings).
- Persistent readouts: Speed, Money, Income/sec, Timer = 4 (+ Stats container and Timer panel).
- => 12 discrete always-on HUD elements (5 groups: Stats, Left, Tabs, Timer, Settings), plus 2 temporary new-player elements (Tutorial card + Meadow-distance pill) = 14 on screen at spawn, before Roblox's own UI. Every button is TextScaled with an icon + label + bevel/inlay images.

## Computed rects (x, y, w, h; right/bottom in parentheses)
### A. 667x375 landscape (small phone, COMPACT)
| Element | rect | text px | flags |
|---|---|---|---|
| Stats | (8,271) 194x96 (r202 b367) | Speed value 29, Money 32, Income 17 | entirely in the joystick zone (x<200, y>175): sits where the thumb/joystick lives |
| Left group | (8,64) 86x172 (r94 b236) | | Quests/Rebirth buttons reach into joystick zone (y>175) |
| Shop button | (13,66) 76x34 | label 17; "R$" badge label 9 | height 34 < 44 |
| Daily | (13,111) 76x34 | 17 | height 34 < 44 |
| Quests | (13,155) 76x34 | 12 | height 34 < 44; text 12 < 14; bottom 14 px in joystick zone |
| Rebirth | (13,199) 76x34 | 12 | height 34 < 44; text 12 < 14; fully in joystick zone |
| Tabs group | (605,108) 54x186 (r659 b294) | | Pets and Index in the jump/action zone (x>467, y>175) |
| Eggs | (609,108) 45x54 | 17 | 45 wide (just above 44) |
| Pets | (609,174) 45x54 | 17 | in jump zone |
| Index | (609,240) 45x54 | 17 | in jump zone; bottom edge y294 vs estimated jump button top ~285 -> ~9 px overlap/crowding |
| Timer | (519,64) 140x38 (r659 b102) | caption 14 (engine 13), value 21 | caption at the 14 px floor |
| Settings | (431,64) 80x34 (r511 b98) | label 20 | height 34 < 44; OVERLAPPED by Tutorial card (see below) |
| Tutorial card (during step 1) | (168,64) 330x55 (r498 b119) | title 22, body 11, counter "1/16" 22 | body text 11 px < 14; card (min-width 330 from USC overrides the 0.4W intent) overlaps Settings by 67 x 34 px (x431-498, y64-98): card is Z40 over Settings Z1, so the Settings label is mostly hidden while step 1 is on (Settings is still clickable because the card is Active=false) |
| Distance pill | (222,123) ~224x30 (r446 b153) | 20 | OK |
| Boosts strip (when a boost is active) | (8,229) 236x34 | | in joystick zone |
| Toasts / hint pill column | top-centre x184..484, below Tutorial/pill | | stacked each frame by HudController |
Touch targets <44 px: Shop, Daily, Quests, Rebirth, Settings (all 34 high); Eggs/Pets/Index are 45x54 (marginal width). Text <14 px: Quests 12, Rebirth 12, R$ chip 9, Tutorial body 11; Timer caption 13-14.
Overlap summary 667x375: Tutorial<->Settings (real, 67x34 px); Stats and Rebirth/Quests inside joystick zone; Pets/Index inside jump zone; Left group (y64-236) and Tabs (y108-294) together take the two screen edges for ~half the screen height of a 375 px display.

### B. 844x390 landscape (large phone, COMPACT)
Stats (8,286) 194x96 (r202 b382); Left (8,64) 86x172 (b236); Shop (13,66) 76x34, Daily (13,111), Quests (13,155), Rebirth (13,199) all 76x34; Tabs (782,108) 54x186 (r836 b294): Eggs (786,108) 45x54, Pets (786,174) 45x54, Index (786,240) 45x54; Timer (696,64) 140x38 (r836 b102); Settings (608,64) 80x34 (r688 b98); Tutorial (254,64) 336x56 (r590 b120) text title 22 / body 11 / counter 22; Distance pill (310,124) ~224x30; Boosts (8,244) 236x34.
Flags: Joystick zone (x<200, y>190): Stats (all), Rebirth (y199-234); jump zone (x>644, y>190): Pets (174-228 partly), Index (240-294). Touch targets <44: same five 34-high buttons. Text <14: Quests 12, Rebirth 12, R$ 9, Tutorial body 11. NO Tutorial<->Settings overlap at this width (18 px gap).

### C. 1920x1080 desktop (BASE profile, mouse/keyboard)
Stats (12,940) 320x128 (r332 b1068): Speed value 39, Money 43, Income 23. Left (12,335) 163x324 (r175 b659): Shop (19,352) 149x65, Daily (19,427), Quests (19,502), Rebirth (19,577) each 149x65; labels Shop 34, Daily 33, Quests 24, Rebirth 22; R$ 19. Tabs (1806,367) 102x475 (r1908 b842): Eggs (1812,440) 91x102, Pets (1812,554), Index (1812,668); labels 33. Timer (1639,994) 267x72 (r1906 b1066): caption 26, value 39. Settings (12,846) 110x44 (r122 b890): label 30. Boosts (12,898) 420x34. Tutorial (582,64) 756x108 (r1338 b172): title 43, body 24, counter 49; Distance pill (848,176) ~224x30, text 20.
Flags: no touch target < 44 (Settings 110x44 is exactly 44 high), no text < 19 px. No overlaps at 1920x1080. HUD occupies the left, right and bottom edges, top-centre only for the tutorial.

### Base-profile caveat (non-touch window 600-653 px high)
Base layout keeps USC minimums (Left min 120x238 centred at 46% height, Settings 110x44 at H-234): for a mouse-driven window with Hud height between 600 and ~653 px, Left's bottom (0.46H+119) passes Settings' top (H-234) and the Rebirth button is covered by Settings. In Studio's own edit viewport (875x565) the controller isn't running, so the base layout is what the screenshots show and Settings visibly overlaps Rebirth (shots 01-10); in the live game H<600 switches to the compact layout instead.

## Menus (modals) - phone sizing
Shop modal (Menus.Shop, Visible=false): Size {0.58,0},{0.84,0}, Pos {0.5,0},{0.53,0}, anchor (0.5,0.5); UIAspect 1.5 (FitWithinMaxSize), USC min 440x290 max 1000x660, UIScale 1. Rect: 667x375 -> (114,52) 440x293 (b345, only 30 px bottom margin; title tab sticks out above y~30, under the topbar band); 844x390 -> (177,44) 490x326; 1920x1080 -> (465,242) 990x660. Close button {0.12,0}x{0.12,0} of the modal = 35x35 px at 667x375 (x529,y39), 39x39 at 844x390, 79x79 at 1920x1080 -> below 44 px on both phones. Row List: ScrollingFrame, in Edit it has 0 rows (only layout children): shop rows are built at runtime by ShopController (buildShop), so the preview screenshot shows an empty dark panel (shot 10). Modal covers Left and Tabs? On 667 wide it spans x114-554, so the Tabs (x605+) stay visible, Left (x8-94) stays visible.
Other modals (Eggs/Pets docked panels {0.264,0},{0.5,0} right-anchored at x 0.992; Index/Daily/Quests/Rebirth/Trails/Fuse/Settings {0.52,0},{0.8,0} centred) were not measured individually.
