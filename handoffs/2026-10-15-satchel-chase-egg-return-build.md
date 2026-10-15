# Build request: Satchel inventory, chase feel, guardians returning eggs (Roll and Steal an Egg!)

**Date:** 2026-10-15
**Written by:** the cloud session. It can't reach Studio.
**Built by:** a local Claude Code session connected to Roblox Studio (place 122907972658396). You are the **director**: you plan, run subagents, check their work, and own the result.
**Repo:** https://github.com/apollo9360/roll-egg-notes, branch `claude/stoic-lamport-jqadwi`.
- Get it with `git fetch origin claude/stoic-lamport-jqadwi`, then `git checkout claude/stoic-lamport-jqadwi`, then `git pull`.
- Commit locally; the user pushes from GitHub Desktop.

**Paste line for the user:** `Read handoffs/2026-10-15-satchel-chase-egg-return-build.md on branch claude/stoic-lamport-jqadwi (git fetch first) and do what it says.`

**Order with the other open handoff:** `handoffs/2026-10-15-retention-monetization-build.md` may still be in progress. **Don't run both in the same place at the same time:** two directors editing one place overwrite each other. If the other one isn't finished, ask the user which to run first.

**Overlaps with that handoff:**
- Its UI (leave overlay, playtime panel, starter packs, hit popup) must hide the hotbar through the hide system built here (feature 1).
- Its hit popup listens for guard catches, which feature 2 changes.

---

## 0. Read first

**Director only**, in full:

| Note | URL |
|---|---|
| Player psychology guidelines | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/18%20-%20Game%20Design/Roblox%20Player%20Psychology%20Guidelines.md |
| StreamingEnabled and large worlds | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/17%20-%20Performance/StreamingEnabled%20and%20Large%20Worlds.md |
| NPC and AI engineering | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/16%20-%20NPC%20%26%20AI/NPC%20and%20AI%20Engineering.md |

**Subagent links.** Give each subagent the ones for its job:

| Note | URL | Who needs it |
|---|---|---|
| UI checklist | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/27%20-%20Checklists/UI%20Checklist.md | feature 1 |
| Mobile UI checklist | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/27%20-%20Checklists/Mobile%20UI%20Checklist.md | feature 1 |
| UI engineering guide | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/07%20-%20UI%20UX/Roblox%20UI%20Engineering%20Guide.md | feature 1 |
| UI modal manager recipe | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/28%20-%20Recipes/Recipe%20-%20UI%20Modal%20Manager%20and%20Button%20Feedback.md | feature 1 |
| NPC and AI engineering | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/16%20-%20NPC%20%26%20AI/NPC%20and%20AI%20Engineering.md | features 2 and 3 |
| Humanoid vs custom NPC controller | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/26%20-%20Decision%20Guides/Decision%20-%20Humanoid%20vs%20Custom%20NPC%20Controller.md | features 2 and 3 |
| Player psychology checklist | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/27%20-%20Checklists/Roblox%20Player%20Psychology%20Checklist.md | features 2 and 3 (critic) |

**What matters from those notes for this work:**
- **The server owns guard decisions and physics.** Gameplay NPCs use `SetNetworkOwner(nil)`.
- **Movement:** use direct `MoveTo` for short open chases and `PathfindingService` for long routes. `Humanoid:MoveTo` times out after about 8 s (`MoveToFinished` fires `false`), so re-issue it every tick while chasing.
- **Brain loops run at a few Hz, not every frame.**
- **Streaming:** the server sees the whole world. Clients may be missing guard or egg parts, so client effects must handle `nil`.

## 1. Facts about the game (from the 2026-10-14 Studio report; re-check each in Studio)

- **Guards:**
  - Meadow has the **Mother Hen**: WalkSpeed 13, WakeSeconds 1.2, a "Hop", and a HitReach value. Riverbank has the **River Croc**; Canyon has its own guard.
  - Server: `ServerScriptService.Server.Services.GuardService`. Client effects: `GuardFxController`, `FeelController` (they act on catch and chase).
- **The steal:**
  - Hold E for 0.6 s to grab the egg. The guard sleeps about 16 studs away and wakes 1.2 s after the grab.
  - A hesitant new player is caught in ~1.5 s today.
  - The steal already has "!", chase music and a "RUN!!" banner. A gold safe line ends the danger.
- **Carry:** the egg grows as it is rolled (`EggService`, `CarryController`, `EggFxController`). Speed is the player stat that sets WalkSpeed.
- **Egg reset:** every 300 s, with 10 s of night. At night start, anyone in the corridor loses their carried egg and is sent home (`EggService` ~lines 1026–1042). Grabbing is refused at night.
- **Today, when an egg is knocked loose** (caught, bat-hit or trapped):
  - inside its guard's zone, it **teleports back to its nest**;
  - outside the zone, the guard **walks back to its own zone**.
  - Find where both happen; probably `EggService` and `GuardService`.
- **Pets** are equipped from a hotbar (the report said "the pet must be equipped from the hotbar"). Find out whether that is the Roblox default backpack (Tools) or a custom hotbar.
- **HUD:** `HudController`, `Hud.*` instances in StarterGui. Find the existing code that hides most of the HUD when a menu or modal opens, or during hatch reveals and shop panels. It may be scattered.

## 2. The three features

### Feature 1: Satchel as the custom inventory, and a single "hide the HUD" switch

**Why:** the hotbar must hide whenever most of the UI hides (opening eggs, the shop, other full-screen moments). The default backpack can't be hidden cleanly at those moments.

**Facts about Satchel** (checked by the cloud session against its GitHub source, `ryanlua/satchel`, v1.4.1):
- **What it is:** an open-source replacement for Roblox's default backpack (hotbar plus inventory). **License: MPL-2.0.** It ships as a **ModuleScript** that a client script `require`s once.
  - It creates `PlayerGui.BackpackGui` (`DisplayOrder` 120).
  - It turns off the core backpack itself: `StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.Backpack, false)`.
- **Dependencies:**
  - it `require`s `script.Parent.topbarplus` (**TopbarPlus**, for its top-bar inventory button), so both must sit side by side;
  - it `require`s its child `Attribution`.
- **Attribution terms** (in `Attribution.client.luau`). Use one of:
  1. keep the Attribution script unmodified and enabled (it prints one line outside Studio);
  2. credit "Satchel" by @WinnersTakesAll in the experience description;
  3. link a DevForum credit topic in the description.
- **Public API** (from the returned module):
  - `:SetBackpackEnabled(enabled: boolean)`: shows or hides the whole thing (hotbar and inventory) and binds or unbinds its hotkeys.
  - `:GetBackpackEnabled(): boolean`
  - `:IsOpened(): boolean`: whether the inventory is open.
  - `:GetStateChangedEvent(): BindableEvent`: fires after the inventory opens or closes.
  - Also `BackpackItemAdded`, `BackpackItemRemoved` and `BackpackEmpty` BindableEvents.
- **Important:** Satchel re-applies `MainFrame.Visible = BackpackEnabled` **every Heartbeat**. Toggling its frames directly won't stick; **always use `SetBackpackEnabled`.**
- **Styling:** attributes on the Satchel ModuleScript: `BackgroundColor3`, `BackgroundTransparency`, `CornerRadius`, `EquipBorderColor3`, `EquipBorderSizePixel`, `InsetIconPadding`, `OutlineEquipBorder`, `TextColor3`, `TextSize`, `TextStrokeColor3`, `TextStrokeTransparency`, `FontFace`.
- **Maintenance:** its README warns that **"Satchel v1 is unmaintained"** while v2 is being built. It recommends **Purse** as a maintained alternative; the cloud session couldn't verify Purse. The user asked for Satchel, so use Satchel unless the user picks Purse in Step B.

**Getting it** (the user asked for this import; it is a game library, not a skill):
- Source: the Creator Store asset **13947506401** ("Satchel", via the Toolbox or an MCP insert), or a GitHub release of `ryanlua/satchel`.
- **Insert it into `ServerStorage.Quarantine` first and audit it before use:**
  - list every script;
  - check every `require`, `HttpService`, `loadstring`, `MarketplaceService` and `TeleportService` call;
  - confirm the version line in `Attribution`;
  - confirm TopbarPlus is included or get it the same way.
- Only then move it to its final place (e.g. `ReplicatedStorage.Packages.Satchel`, with TopbarPlus beside it) and require it from one client controller.
- **Standing-rule exception:** Satchel builds its own UI from code. The user's rule is "UI is real instances; controllers only bind". Accept that for this one library, and note it in DEV_NOTES.

**The hide switch (build this; it is the real point of feature 1):**
- **One client module owns "is the HUD hidden?"**, e.g. `HudVisibility` with `hide(reason: string)` / `show(reason: string)` and a set of active reasons. The HUD and hotbar are visible only when **no reason** is active. A reason count, not a boolean, so two overlapping screens don't un-hide each other.
- **When the reason set changes:**
  - call `Satchel:SetBackpackEnabled(isEmpty)`;
  - set the other HUD elements' visibility the same way the existing hide code does (reuse it, then route it through this module).
- **Every place that hides most of the UI today goes through it,** including:
  - hatch / egg-opening reveals;
  - the shop and other full-screen modals;
  - the Index;
  - tutorial spotlight moments, if they hide the HUD;
  - anything else the audit finds.
- **New screens from the retention handoff,** if built, register too: playtime panel, starter packs, leave overlay.
- **Inventory vs HUD:** if a full-screen screen opens while Satchel's inventory is open, close the inventory first (`IsOpened()` / its open-close toggle), then hide.
- **Migration:**
  - If pets are Tools in the default backpack today, Satchel takes over with no change to the Tools.
  - If there is a **custom hotbar**, report it and ask the user (Step B) whether Satchel replaces it or just hosts Tools beside it.
  - Keep equip and placement working exactly as before, including the tutorial's "Place your pet" step.
- **Look:** style Satchel through its attributes to match the game (load the art-direction skill first). Check phone tap sizes and that the hotbar doesn't cover the jump button or joystick.

### Feature 2: chase feel in three phases

**The user's description, verbatim:**
> Start: The defender wakes up. You run away, and it looks like you're getting fully away with it. Next: The defender moves towards your position incredibly fast. This is regardless of if you're faster than it or not. Finally, during the chase, the defender should always be right on your tail, even if you outspeed it a ton.

**Spec** (server-driven, per guard; every number lives in config per guard type):

| Phase | What happens | Suggested numbers |
|---|---|---|
| 1. Getaway | The guard wakes (the existing "!" and wake animation) and stays nearly still or slow, so the player pulls away and feels they've got away with it. | `GetawaySeconds` ≈ 1.0–1.5 s after wake; guard speed ≈ 0–30% of its normal speed |
| 2. Lunge | The guard rushes toward the player at a speed **independent of the player's speed**, until it is `TrailDistance` behind them. Use a big wind-up cue (sound, dust, camera kick on the client) so it reads as deliberate. | Close the gap in ≈ 0.6–1.0 s whatever the distance, capped at e.g. 120 studs/s |
| 3. On your tail | The guard holds `TrailDistance` behind the player however fast they are (rubber band), until the chase ends. | `TrailDistance` ≈ 5–7 studs, just outside `HitReach` |

- **Phase 3 follows the player's path, not a straight line.**
  - The server keeps a short breadcrumb trail of the carrier's positions (e.g. every 0.1 s, last 3 s).
  - The guard moves along that trail to the point `TrailDistance` back. This keeps it out of walls and makes it look like it's really chasing.
- **Speed in phase 3:** set the guard's WalkSpeed each tick from how far it is from that point (faster when behind, slower when too close).
- **Phase 2:** use the breadcrumb trail too, or a direct `MoveTo` if the path is clear.
- **If a Humanoid can't keep up at very high player speeds,** move the guard by CFrame or an `AlignPosition` / `LinearVelocity` mover. The "Humanoid vs custom NPC controller" note covers the trade-off.
- **Network:** keep `SetNetworkOwner(nil)`. Check how smooth it looks on a client at high speed; if it stutters, report it with options rather than moving guard control to clients.
- **When does the guard catch someone?** "Always on your tail" means it would never catch a fast player. **Ask the user (Step B).** Recommended:
  - the guard trails just outside reach;
  - it **catches only** if the player slows or stops (their speed drops below the guard's normal chase speed for ~0.5 s), gets stuck, or turns back into it;
  - near misses are fine, and good for the feeling of the chase;
  - the planned first-timer grace (slower wake, no catch) still applies to brand-new players.
- **The chase ends** exactly where it does today (safe line or leaving the guard's zone). The guard then walks home as it does now.
- **Client feel** (`GuardFxController` / `FeelController`): chase music swells at the lunge; a quick camera kick and FOV punch at the lunge. While trailing, add a subtle heartbeat or footstep layer. Keep effects short on phones.
- **Performance:** one brain loop per active chase at ~10–20 Hz, not per frame. Sleeping guards cost nothing.

### Feature 3: a guard picks up a loose egg in its zone and carries it home

**Today:** a loose egg inside its guard's zone teleports to the nest; outside, the guard walks home.

**Wanted:**

| Where the loose egg is | What happens |
|---|---|
| Inside its guard's zone | **The guard walks to the egg, picks it up, carries it back to the nest and puts it back.** |
| Outside the zone | The guard walks back to its own zone, **unchanged**. Keep today's outside-zone behaviour for the egg too; confirm what that is in the audit. |

**Spec:**
- **Server-owned states:** e.g. `Sleeping → Chasing → Fetching → Carrying → Returning → Sleeping`.
  - Fetching uses pathfinding if the egg isn't in a straight open line.
  - Pick-up: a short animation, then weld or attach the egg to the guard (beak, jaws or hands, whatever fits the model). Carrying shows it clearly.
  - At the nest: put the egg back in its nest slot (reuse the nest's existing "add egg" code), then sleep.
- **Fairness and edge cases** (decide the open ones in Step B):
  - **A player grabs the loose egg before the guard reaches it:** allowed (a race). The guard switches straight to Chasing (phase 3 of feature 2; skip the getaway).
  - **Can a player take the egg from a guard that's carrying it?** Recommend **no** (simpler, and it reads as "the guard got it back"). Ask the user.
  - **The egg's size and growth while loose or carried:** keep whatever the egg had. The nest copy follows the current rules for re-nested eggs.
  - **Several loose eggs:** fetch the nearest first, then the next.
  - **Stuck or unreachable:** if the guard hasn't reached the egg (or the nest) within e.g. 8 s, fall back to today's teleport so nothing is lost.
  - **Night or egg reset during a fetch:** the reset wins. Clear the carried egg as the reset clears eggs today, and send the guard to sleep.
  - **The egg rolls out of the zone before pickup:** switch to the outside-zone behaviour.
- **Client:** a small "!" or carry animation, so players see the guard took it back. Nothing that needs the egg part to be streamed in.
- **Interaction with the onboarding funnel and tutorial:** if the tutorial resets on a catch today, check that the new return flow doesn't break any tutorial step (e.g. the first steal's nest egg).

## 3. Method (director)

### Step A: fact base (read-only), in parallel

Launch **three audit subagents at once**, one per feature. They only read: `script_read` / `script_grep` / `search_game_tree` / `inspect_instance`, and `loadstring(Source)` for compile checks.

**Prompt template:**

```text
You are auditing Roll and Steal an Egg! in Roblox Studio for ONE planned feature. READ-ONLY: no edits,
no playtests, no require() of server modules. Studio: list_roblox_studios → place 122907972658396.
Feature: <PASTE THE FEATURE SECTION FROM THE HANDOFF>.
Game facts (verify, don't trust): <PASTE SECTION 1>.
Read these first (fetch the URLs): <PASTE THE LINKS FROM SECTION 0 FOR THIS FEATURE>.
Return:
1. The current behaviour, traced through the code: exact paths, line numbers, quoted lines.
   (Feature 1: every place that hides/shows HUD parts, and how pets/Tools are equipped today.
    Feature 2: the guard state machine, wake, chase, catch and HitReach code, and per-guard config.
    Feature 3: where loose eggs teleport home, where guards walk home, how zones are defined.)
2. The systems the change must reuse, and anything it could break (tutorial steps, funnel events,
   night reset, other handoffs' features).
3. Streaming and performance risks.
4. Questions only the user can answer, each with your recommendation.
5. A build plan: ordered steps with files, server vs client, effort XS–L.
Say "not found" with where you looked rather than guessing.
```

**Then run one skeptic subagent over the three audits:** "Re-check every path, line number and claim from the code yourself. Mark each CONFIRMED or WRONG, with evidence." Write the fixed fact base to `handoffs/2026-10-15-satchel-chase-egg-return-audit.md`.

### Step B: one decision message to the user

| # | Question | Recommendation |
|---|---|---|
| Q1 | Satchel or Purse? (Satchel v1 is unmaintained) | Satchel, as asked |
| Q2 | Satchel attribution: keep the Attribution script, or credit in the description? | Keep the script (one print line outside Studio) |
| Q3 | If a custom hotbar exists: replace it with Satchel, or keep it? | Replace it, if equipping keeps working |
| Q4 | When does a trailing guard catch a player? | Only when the player slows or stops (~0.5 s), gets stuck, or turns into it; first-timer grace stays |
| Q5 | Chase numbers per guard (getaway, lunge time, trail distance). Show your proposed table | ~1.2 s getaway, ~0.8 s lunge, 6 studs trail |
| Q6 | Can a player take an egg back from a guard that's carrying it? | No |

### Step C: build

**Order:**
1. Feature 1: the hide switch first, then Satchel.
2. Feature 3.
3. Feature 2. It touches the same guard code as feature 3, so build them one after the other, never in parallel.

**For each feature:**
1. Reuse what the audit found. No second guard loop, second HUD hide path or second nest API.
2. **Server-authoritative:** the server owns guard states, catches, pick-ups and egg placement.
3. Put every tuning number in config, per guard type.
4. Run a compile check (`loadstring(Source)` via `execute_luau` in Edit mode) and take a `screen_capture` for anything visual.
5. **Run critic subagents, in parallel, on the finished feature:**
   - **UI critic** (feature 1): UI and Mobile UI checklists, plus a screenshot with each hide reason active.
   - **Gameplay critic** (features 2 and 3): trace the state machine for every edge case listed above. Report any path that leaves a guard stuck, an egg lost or duplicated, or a player caught unfairly.
   - **Performance and security critic:** loop rates, connection cleanup, network ownership, no client-trusted positions.
   Fix what they confirm.
6. Add a `DEV_NOTES.md` entry, newest first, under `## Changelog`.

## 4. Rules (from the user; mandatory)

- **NEVER download or install anything as a skill** (or plugin) unless the user explicitly said so beforehand. Satchel (and TopbarPlus, its dependency) is a **game library the user asked to import**. That's allowed, through Quarantine and an audit.
- **No playtests without the user's permission.** The user playtests and reports. Chase feel especially needs the user's playtest; build it with the numbers in config so they can be tuned.
- **Before anything visual,** load the user's installed skill `roll-egg-art-direction` (`~/.claude/skills/roll-egg-art-direction/SKILL.md`) and follow it.
- **Never move or rebuild the map.** Inspect before editing; don't override the user's changes.
- **Reference folders** (`REFERENCE_*`, `Quarantine`, `Snapshots_*`, `W1_Backups`, the decomp):
  - you may read them;
  - you may use their image, sound and mesh assets only after the open-use check in `handoffs/2026-10-15-retention-monetization-build.md` (section 3, Step A0);
  - never copy their scripts, UI layouts or models.
- **No Creator Hub.** Never click through it or enter credentials.
- **Edit-mode `require` caches stale modules.** Verify with `loadstring(Source)` or a clone parented next to its siblings.
- **UI is real instances in StarterGui; controllers only bind.** The one exception is Satchel's own generated UI (see feature 1).

## 5. Stop points and finish

1. **After Step A:** send the Step B message and wait. Build only answered items. Skip anything unanswered and keep going.
2. **When done:**
   - Commit on `claude/stoic-lamport-jqadwi`:
     - the audit file;
     - a report at `handoffs/2026-10-15-satchel-chase-egg-return-report.md`;
     - screenshots in `handoffs/2026-10-15-refs/built-satchel-chase/`.
   - Tell the user it's ready to push.
3. **The report covers:**
   - the decisions made;
   - what was built (paths);
   - the Satchel audit result and attribution choice;
   - the hide-reason list;
   - the chase config table;
   - the guard state diagram with every edge case and what happens;
   - compile results;
   - critic findings and fixes;
   - a playtest checklist for the user, covering:
     - every hide reason (hotbar gone, then back);
     - equip and place a pet;
     - the tutorial steal;
     - a chase while slow, while very fast, and stopping mid-chase;
     - a bat-hit or trap inside the zone (the guard fetches the egg), a player racing the guard to the loose egg, and a loose egg outside the zone;
     - night falling during a fetch;
     - a phone pass in Device Simulator.
