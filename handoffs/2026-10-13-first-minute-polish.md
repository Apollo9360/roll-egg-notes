# Handoff: first-minute polish (Roll and Steal an Egg!)

**Date:** 2026-10-13

**Studio:** place 122907972658396, studio_id `7ec6b932-e26d-4b1a-aade-6a34ae9394b5`. A second Studio called "test" (the Steal an Egg decomp) may also be open. It is a visual reference only.

## Why this exists
The user is running ads that target Simulation and Party/Casual players. One new player walked from spawn toward the eggs and quit about halfway, around 5–8 seconds in.

The diagnosis from the previous session:
- **The tutorial card is not the cause.** On step 1 it is a thin bar at the top of the screen (Hud.Tutorial, Size {0.5,0},{0.1,0}).
- **The spawn view looks unfinished.** These lobby signs show placeholder or stale text, and no script updates them:
  - `Workspace.World.Lobby.EntranceArch.Sign.SurfaceGui.Label` = "XX:XX to next HUGE EGG". It sits right in front of spawn.
  - `Workspace.World.Lobby.BiggestEgg.BiggestEggBoard.SurfaceGui.Label` = "Biggest egg today\n-". The board is at (163, 7, 204).
  - `Lobby.TrailStand.HeaderBoard.HeaderGui.Subtitle` and `Pedestal1..6.Sign.SignGui.Effect` still say "treadmill". The game now uses hamster wheels.
- **The walk is long and empty.** LobbySpawn is at (188, 0.5, 256) and the first Meadow nest is at about (226, 0, 95). That is ~165 studs, roughly 10 s at walk speed 16.
  - Halfway is the safe-zone line (Meadow GatePosts at z≈176), followed by an open field.
  - The golden `Lobby.EggStatue`, an 18×18×18 model at (188, 9, 224), stands directly in front of spawn and blocks the view toward the nests.
- **There is no analytics at all.** Nothing calls AnalyticsService, so there is no drop-off data.

## Goal: build all four (plus the sign text fix)

### 1. HUGE EGG timer on the entrance arch
- **No HUGE EGG system exists yet.** Grep finds no "HUGE EGG" logic. `Config.SizeTiers` has a "Huge" size tier (MinRatio 0.85, Scale 1.6), which is a size class, not an event.
- **Open question: ask the user before building.** What happens when the timer hits 0?
  - Suggested default: on a fixed cadence, one nest spawns a guaranteed Huge-tier (or bigger) egg with an announce, a beam and a sound.
  - The cadence could be every N egg resets. WorldService already runs the egg reset cycle (Workspace `CyclePhase`, and the HUD's "NEW EGGS IN 5:00").
- **Build pattern:**
  - The server owns the schedule and publishes a Workspace attribute, e.g. `HugeEggAt` (os.time / workspace:GetServerTimeNow).
  - A client controller renders the arch label as a countdown.
  - Reuse WorldService's timing; don't add a second clock.

### 2. "Biggest egg today" board: show the holder in 3D, holding the egg
- **Read `ServerScriptService.Server.Services.LeaderboardService` first.** There is already a BIGGEST EGG leaderboard at `Lobby.Retention.Leaderboards.BiggestEgg` (rows show "-" / "Loading..." in Edit mode). Find out how it tracks egg size.
  - If it is all-time, "today" needs a daily key (e.g. an OrderedDataStore keyed by UTC date).
- **Display:**
  - Next to the BiggestEgg board, put a real avatar: `Players:CreateHumanoidModelFromUserId` inside pcall (it works for offline users).
  - Pose it holding a scaled copy of that egg, using the same egg visuals the carry system uses (see CarryController / EggFxController). Anchor the model and give it an idle animation.
  - Show the name and size on the board.
  - Have a fallback for "no egg yet today" (a placeholder pose or a hidden rig) and for the avatar load failing.
- Refresh on a slow interval. Respect DataStore budgets.

### 3. Onboarding funnel analytics
- **Server only.** Use `AnalyticsService:LogOnboardingFunnelStepEvent(player, step, stepName)`.
- **What to log:**
  - Steps must be sequential from 1, and each is logged once per player.
  - Log only for genuinely new players (TutorialStep nil/0 on join), so returning players don't pollute the funnel.
- **Suggested steps:**
  - 1 Joined
  - 2 Left spawn area
  - 3 Crossed the safe line into the Meadow
  - 4 Reached the nest
  - Then each tutorial step from `Config.Tutorial.Steps`: Steal, Escape, Home, Place, Hatch, BuyWheel … Trail, Done.
  - Hook those into `TutorialService.setStep`.
- **Data reality:** step indices are saved and append-only (Done = #Steps + 1 = 17). The funnel numbering can be its own mapping.
- **Optionally** add `LogProgressionEvent` / custom events later. Funnel first.

### 4. Make the walk *feel* shorter (never move the map)
- **Best lever: give the walk content.**
  - Put a line of collectible pickups along spawn → Meadow nest for brand-new players (tutorial step 1 only). Each one ticks the Speed stat using the existing speed-gain fly-to-HUD animation (SpeedFxController).
  - The server grants a tiny amount and validates distance; don't trust the client.
- **Supporting ideas:**
  - Ground chevrons that light up in sequence toward the nest.
  - The card shows the distance left ("Meadow nest: 80 studs").
  - The nest egg and the Mother Hen bounce or glow so they're readable from spawn.
  - A whoosh and sound when crossing the safe line.
- **EggStatue blocking the sightline:** moving it counts as changing the map, so **ask the user** before moving or shrinking it.

### 5. Sign text
- Change "treadmill" to "wheel" on the TrailStand header and pedestal signs.
  - Check whether TrailStandController sets these at runtime first.
  - If not, edit the instances in place.

## Standing rules (from the user; mandatory)
- **Don't playtest.** The user playtests and reports.
  - Verify statically: loadstring(Source) compile checks via execute_luau in Edit mode, plus screen_capture.
  - Screenshots during the user's play session are OK.
- **UI and world objects are real instances** in StarterGui/Workspace. Controllers only bind.
- **Never rebuild or move the map. Don't override the user's changes.** Inspect before editing.
- **Before anything visual,** load the user skill `roll-egg-art-direction` (~/.claude/skills/roll-egg-art-direction/SKILL.md).
- **Toolbox assets** go into a Quarantine folder and get audited before use.
- **Consult-only:** ServerStorage reference folders (REFERENCE_*, Snapshots_*, W1_Backups, the decomp). Never copy them.
- **Creator Hub items:** if anything needs them (badges, products), give the user a list. Never click through Creator Hub; never enter credentials.
- **Log every change in `DEV_NOTES.md`** (scratch workspace root), newest first, under `## Changelog`.
- **Edit-mode `require` caches stale modules.** Verify with loadstring(Source) or a clone parented next to its siblings. `Config.Tutorial` requires `script.Parent.Trails`, so a parentless clone errors.

## Relevant code
- **Server:** `ServerScriptService.Server.Services`:
  - WorldService (egg reset cycle, CyclePhase)
  - LeaderboardService
  - TutorialService (setStep, steps 1–16, Done = 17)
  - EggService, GuardService, SpeedService, RetentionService
- **Client:** `StarterPlayer.StarterPlayerScripts.Client.Controllers`:
  - TutorialController (card, beam, spotlight)
  - SpeedFxController (speed fly-to-HUD)
  - HudController, CarryController, EggFxController, TrailStandController
- **Config:** `ReplicatedStorage.Shared.Config`: Tutorial, Economy, Biomes, SizeTiers, Trails.
- **World attributes:** `workspace.World` attribute `TutorialTrailSpot`.

## Suggested order
1. Ask the user both open questions: HUGE EGG payoff/cadence, and whether the EggStatue may move.
2. Onboarding funnel. It's the smallest change and starts collecting data immediately.
3. Sign text.
4. Walk pickups and cues.
5. HUGE EGG timer.
6. Biggest-egg 3D display.

Compile-check after each one and add a DEV_NOTES entry.

## Return
Report back:
- what was built (instance and script paths);
- compile results and before/after screenshots of the spawn view;
- the funnel step list as implemented;
- a playtest checklist for the user.
