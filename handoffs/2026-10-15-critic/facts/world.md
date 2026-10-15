# World facts (read-only inspection of Studio edit DataModel, place "Steal and Roll an Egg!", placeId 122907972658396)

All numbers measured from the instances in Edit mode on 2026-10-13. No playtest was run, no server module was require()'d. Straight-line (flat XZ) distances: real routes can be longer (fences, curtain, walls); seconds = studs / WalkSpeed.

## Walk speed at start
- Economy.StartingSpeed = 10. Curves.WalkSpeedFromSpeed(speed) = min(16 + Slope*log10(speed/10), 160) (BaseWalkSpeed 16, MaxWalkSpeed 160, source: Shared.Config.Economy / Shared.Curves).
- So a new player (Speed 10) has Humanoid.WalkSpeed = exactly 16. "At WalkSpeed 16" and "at starting Speed" are the same number; one column below.
- Carry drag (egg weight) and slow effects multiply this down (SpeedService.GetExpectedWalkSpeed = base * drag * slow), so times coming back WITH an egg are longer than these.

## Where a player actually spawns
- Engine spawn = `Workspace.World.Lobby.LobbySpawn` (SpawnLocation, Enabled, Neutral=true, Duration 10, size 8x1x8) at (188.25, 0.5, 256.25), LookVector (-1,0,0) i.e. faces WEST (-X). It is the only SpawnLocation in the place.
- PlotService.spawnAtPlot (ServerScriptService.Server.Services.PlotService, ~line 184-210): after CharacterAdded it waits for the character to be in Workspace + 1 frame, calls Player:RequestStreamAroundAsync(plot.Spawn, 2), then `character:PivotTo(CFrame.lookAt(plot.Spawn, plot.Spawn + plot.SpawnFacing))`. plot.Spawn = Plot.Spawn part position + (0,3,0); SpawnFacing = Spawn part LookVector flattened. This repeats on every respawn. If no plot is free the player stays at LobbySpawn (only a warn "No free plot").
- Plots are assigned first-come in index order 1..5 (PlotService.assign). Only 5 plots exist; Players.MaxPlayers reads 60 in Studio and no server code caps the server at 5 (the real cap lives in Creator Hub settings and is not readable here). Players 6+ would have no plot, no pen, no wheel pad.

| Plot | Spawn part pos (x, y, z) | Faces (LookVector) | Notes |
|---|---|---|---|
| Plot1 (typical, centre of south row) | (188.25, 0.15, 271.25) -> character at y=3.15 | (0,0,-1) = north, toward EntranceArch / Meadow gate | Spawn sits on the Porch IN FRONT of the plot; the plot floor is BEHIND the player (floor centre (188.25, 0.05, 307.25), 52x46) |
| Plot2 | (138.25, 0.15, 271.25) | (0,0,-1) | same layout |
| Plot3 | (238.25, 0.15, 271.25) | (0,0,-1) | same layout |
| Plot4 (west) | (110.25, 0.15, 238.98) | (1,0,0) = east, toward plaza | floor centre (74.25, 0.05, 238.98) |
| Plot5 (east) | (266.25, 0.15, 241.62) | (-1,0,0) = west, toward plaza | floor centre (302.25, 0.05, 241.62) |

So a typical new player (Plot1-3) opens their eyes on the porch, looking north down the lobby at: EggStatue (188.25, 224), the huge-egg/"BiggestEgg" podium (163.5, 204), the EntranceArch (188.25, 188) and, beyond it, the Meadow. Screenshot 01 reproduces this (camera 12-14 studs behind spawn).

## Key landmarks (world positions, XZ)
- Meadow biome ground: centre (188.25, 116.25), 120 (along corridor) x 150 (across) -> spans z 56.25..176.25, x 113.25..263.25. Corridor runs toward -Z; "home" is +Z.
- Meadow SAFE LINE / gate ("lock line", WorldIndex.LockLineX = corridor.MinX = Meadow home edge) = z 176.25. Marked by Biomes.Meadow.ZoneEdge.Line (150 wide gold line at z=176.25), a light "Curtain" part (11.1 deep x 3.8 high, z 181.7) and floor text "SAFE ZONE" on the home side; GatePost1/2 at x=115.25 and x=261.25 (so the "gate" is a 146-stud-wide opening, not a door); a giant 18-high lintel is the EntranceArch of the lobby at z 188. EntranceSign part sits at y=-84 (below ground, not visible).
- Meadow nest centre = average of 5 nest tops = (226.25, 0.6, 96.25) (Nests: (226.2,90.2) (232.0,94.4) (229.8,101.1) (222.7,101.1) (220.5,94.4)); NestArea trigger part 20x20 at (226.25, 0.1, 96.25). So the nest is 80 studs INSIDE the Meadow past the safe line and at the far end of the Meadow ground.
- Mother Hen = Workspace.World.Guards.Guard_Meadow, centred (234.25, 2.5, 82.25) (GuardPost part (234.25, 0.1, 82.25)), model ~8.4 wide x 13.5 tall x 11.7 long, ModelStreamingMode Atomic. It is 16 studs from the nest centre, 11 from Nest1.
- Own plot: Floor centre = see table above; Pen centre (Plot1) (188.4, 0.1, 307.3) 50.5x45.2 studs; WheelPad (the hamster wheel footprint, 13x1.2x9) Plot1 (176.25, 1.15, 272.85) i.e. immediately next to the spawn.
- Hamster-wheel board = Plot<N>.Boards.TreadmillBoard (SurfaceGui text "HAMSTER WHEEL / Level 1 > Level 2 / $100"), Plot1 at (167.75, 5.95, 279.65); the Pen board (PenBoard, "UPGRADE PEN $100") at (200.25, 5.95, 279.65). Both stand on the fence line just north-in-front of the plot. Plot2 (117.75 / 150.25, 279.65), Plot3 (217.75 / 250.25, 279.65), Plot4 (101.85, 218.48 / 250.98), Plot5 (274.65, 262.12 / 229.62).
- Trail Stand: Workspace.World.Lobby.TrailStand (Atomic streaming), bounding-box centre (242.93, 8.08, 203.75), 37 x 31 studs footprint, 6 pedestals: Green Trail x1.5 $5.0K, Blue x2 $75.0K, Purple x3 $1.0M, Gold x4.5 $15.0M, Galaxy x6 $250M, Rainbow x8 $4.0B (tutorial step 16 targets the Green $5.0K one). Catalog prompt at (228.5, 4.0, 218.5).
- Fuse Machine: World.Lobby.Retention.FuseMachine at (129.54, 0.5, 205.12), 14 x 12 footprint, 14.5 high.
- Other: Lobby.BiggestEgg board (163.48, 9, 204.25); Lobby.EggStatue (188.25, 224.25); Lobby.EntranceArch (188.25, 188.25); NestMarker (FirstWalk) (226.25, 96.25).

## Distances (flat studs / seconds at WalkSpeed 16 = starting Speed 10)
| From | Meadow nest centre | Safe line (nearest point z=176.25) | Gate centre (188.25,176.25) | Own plot floor centre | Own pen | Own wheel pad | Own hamster-wheel board | Trail Stand | Fuse Machine |
|---|---|---|---|---|---|---|---|---|---|
| Plot1 spawn (typical) | 179 / 11.2 s | 95 / 5.9 s | 95 / 5.9 s | 36 / 2.3 s | 36 / 2.3 s | 12 / 0.8 s | 23 / 1.4 s | 87 / 5.4 s | 88 / 5.5 s |
| Plot2 spawn | 196 / 12.2 s | 95 / 5.9 s | 107 / 6.7 s | 36 / 2.3 s | 36 / 2.2 s | 12 / 0.8 s | 23 / 1.4 s | 125 / 7.8 s | 67 / 4.2 s |
| Plot3 spawn | 175 / 11.0 s | 95 / 5.9 s | 107 / 6.7 s | 36 / 2.3 s | 36 / 2.2 s | 12 / 0.8 s | 23 / 1.4 s | 68 / 4.2 s | 127 / 8.0 s |
| Plot4 spawn | 184 / 11.5 s | 63 / 3.9 s | 100 / 6.3 s | 36 / 2.3 s | 36 / 2.2 s | 12 / 0.8 s | 23 / 1.4 s | 137 / 8.6 s | 39 / 2.4 s |
| Plot5 spawn | 151 / 9.4 s | 65 / 4.1 s | 102 / 6.4 s | 36 / 2.3 s | 36 / 2.2 s | 12 / 0.8 s | 23 / 1.4 s | 44 / 2.8 s | 142 / 8.8 s |
| LobbySpawn (188.25, 256.25) | 164 / 10.3 s | 80 / 5.0 s | 80 / 5.0 s | Plot1: 51 / 3.2 s | - | Plot1: 21 / 1.3 s | - | 76 / 4.7 s | 78 / 4.9 s |

Extra: LobbySpawn -> Plot1 Spawn 15 studs / 0.9 s. Plot1 spawn -> EggStatue 47 / 2.9 s, -> BiggestEgg board 71 / 4.5 s, -> EntranceArch 83 / 5.2 s.
Read-across: first-ever objective (steal from the Meadow nest) is ~11 s of straight running from a typical spawn (about 6 s to reach the safe line, then ~5 s more inside the Meadow); first hamster wheel is under 1 s from spawn; the Trail Stand and Fuse Machine are 4-9 s away depending on plot. Return trip with an egg is slower (drag).

## Workspace / instance budget
- Workspace children: World (Folder, 12,990 descendants), TopbarPlus (Folder, 26; no script in the game references "Topbar" - orphan), Terrain, Atmosphere (Part, 48 - see particles), Model (57, contains "Spikes"), WaterA (33), Camera.
- Workspace descendants: 13,161. BaseParts: 10,506 (Part 10,487; MeshPart 16; UnionOperation 1; SpawnLocation 1). Anchored 10,491 / unanchored 15. CanCollide true on 895. CastShadow true on 3,812. Models 1,668. Whole DataModel instance count: 54,115 (includes ServerScriptService, ReplicatedStorage assets/pets, StarterGui ~1,360, StarterPlayer incl. 150+ UI instances in CustomPrompts/TEST).
- Biggest sub-trees: Biomes 8,023 (Meadow 897, Riverbank 1,485, Canyon 1,291, Snow 1,359, Volcano 1,578, Cosmic 1,407), Walls 1,672 (BrickWalls 1,667), Plots 1,876 (~372-377 each), Lobby 1,101, Guards 312 (each guard is 31-59 anchored Parts), Eggs 0.
- Number of plots: 5 (Plot1..Plot5). Players.MaxPlayers = 60, PreferredPlayers 60.
- Lights: PointLight 5 (all enabled), SpotLight 0, SurfaceLight 0. ParticleEmitters 66 (all Enabled), Beams 8 (all Enabled), Trails 0. 40 of the 66 emitters sit on ONE invisible Part `Workspace.Atmosphere` (307 x 70 x 1586 studs, centre (207, 33, -447), Transparency 1) - fog/star/debris/"wisp" emitters spanning the Riverbank..Cosmic corridor. Also: Attachments 56, ProximityPrompts 14, SurfaceGuis 39, BillboardGuis 24, Textures 27, Highlight 1, Humanoid 1 (the BiggestEgg PlaceholderRig dummy), Scripts: 1 Script + 21 ModuleScripts under Workspace.

## Streaming
- Workspace.StreamingEnabled = true. StreamingMinRadius / StreamingTargetRadius / StreamOutBehavior / StreamingIntegrityMode are not readable through the Studio MCP (properties report as unreadable) -> assume Roblox defaults; no script in ServerScriptService/ReplicatedStorage/StarterPlayer/ServerStorage assigns StreamingMinRadius/StreamingTargetRadius (grep, 0 hits).
- The code is designed for streaming: WorldIndex (server) publishes world layout as attributes under ReplicatedStorage.WorldData so the client never waits for far biome parts; PlotService calls RequestStreamAroundAsync(plot.Spawn, 2) before teleporting.
- ModelStreamingMode: Plots Plot1-5 = Persistent; Guards Guard_* (all 6) = Atomic; Lobby.TrailStand = Atomic (LevelOfDetail StreamingMesh); Lobby.BiggestEgg / EntranceArch / EggStatue = Default; all 6 Biome models = Default (LevelOfDetail Automatic); Workspace.Model and Workspace.WaterA = Persistent.
- The `Atmosphere` Part is a direct Workspace child with default (non-persistent) part streaming, 1.6 km long: it only exists on a client when streaming brings its single box in range.

## Lighting
- Lighting.Technology is NOT readable through the MCP API (property access errors). Attributes on Lighting: RBX_OriginalTechnologyOnFileLoad = 3 (ShadowMap at file load) and RBX_LightingTechnologyUnifiedMigration = true -> treat as ShadowMap/Future-era unified lighting; confirm in Studio Properties if it matters.
- GlobalShadows = true; Brightness 2.5; ClockTime 11.6 (TimeOfDay 11:36:00); Ambient (0.855, 0.780, 0.612); OutdoorAmbient (0.573, 0.788, 0.886); EnvironmentDiffuseScale 0.75; EnvironmentSpecularScale 0.70; ExposureCompensation 0; ShadowSoftness 0.2; FogEnd 100000 (effectively no fog) FogStart 0.
- Active post/atmosphere effects (direct children of Lighting): Atmosphere (Density 0.309, Haze 0, Offset 0), Sky (rbxassetid://591058823), ColorCorrection (Enabled; Contrast 0.2, Saturation 0.1, Brightness 0), SunRays (Enabled; Intensity 0.01, Spread 0.5 - nearly invisible). NO Bloom is active and no DepthOfField is active.
- Lighting also contains: a `Folder` stashing a second Atmosphere/Sky/Bloom/DepthOfField/SunRays set (inert, not applied) and THREE DepthOfFieldEffects all named "TabControllerDepthBlur", Enabled=false (leftover from a tab/blur helper; harmless but clutter and a possible duplicate-name source of bugs).
