# Build request: five retention and monetization features (Roll and Steal an Egg!)

**Date:** 2026-10-15
**Written by:** the cloud session. It can't reach Studio.
**Built by:** a local Claude Code session connected to Roblox Studio (place 122907972658396). You are the **director**: you plan, run subagents, check their work, and own the result.
**Repo:** https://github.com/apollo9360/roll-egg-notes, branch `claude/stoic-lamport-jqadwi`.
- Get it with `git fetch origin claude/stoic-lamport-jqadwi`, then `git checkout claude/stoic-lamport-jqadwi`, then `git pull`.
- The branch was restarted from `main` on 2026-10-15. If your local copy has unpushed commits, merge them in with `git pull` (no rebase).
- Commit locally; the user pushes from GitHub Desktop.

**Paste line for the user:** `Read handoffs/2026-10-15-retention-monetization-build.md on branch claude/stoic-lamport-jqadwi (git fetch first) and do what it says.`

---

## 0. Read first

**Director only.** Read these two in full before planning:

| Note | URL |
|---|---|
| Player psychology guidelines | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/18%20-%20Game%20Design/Roblox%20Player%20Psychology%20Guidelines.md |
| StreamingEnabled and large worlds | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/17%20-%20Performance/StreamingEnabled%20and%20Large%20Worlds.md |
| Steal an Egg teardown (the reference game; the user's own research page) | https://claude.ai/artifact/MAhNhAvKyDcWYoTao8CpNX |

**If you can't open the claude.ai teardown link,** the lessons that matter for these five features are summarised here, from its data as of 11 Oct 2026:

- **Steal an Egg runs on timers, the 5-minute reset and offline hatching.**
  - It has **no daily login rewards, streaks, quests or codes**, and the teardown says it weakens at days 8–28.
  - Playtime rewards (feature 2) and the freebie egg (feature 1) fill exactly that gap.
- **Its first purchase is a 3 R$ "x2 Speed" HUD offer.**
  - Its paid ladder includes Speed packs (79 → 2,999 R$) that skip whole biomes. YouTubers call it pay-to-win, and the teardown warns this "empties out the core loop".
  - Our starter packs give fixed, non-scaling items, so they help early without replacing play.
- **Getting bat-hit or trapped in the first 2–3 minutes is a top quit reason** (seen in four languages), along with "no private servers".
  - So the on-hit popup (feature 4) must never show during the tutorial or the new-player grace window. A sales pitch right after a griefing hit must not feel like mockery: keep it short and closable.
- **Mobile:** side-by-side cash and Robux buttons caused an accidental Robux purchase on camera. Give every Robux button a different shape and colour from cash buttons, large touch targets, and the confirm step.
- **Make outcomes legible.** Players couldn't find odds, mutation status or trail multipliers in-game, and guides disagree on every number. Show the numbers in the playtime panel, the packs and the ODDS button.
- **Offline hatching and long timers are its main day-1 return reason.** The leave overlay (feature 5) and the freebie egg lean on the same pull, honestly.

Why the StreamingEnabled note matters here: the freebie egg sits on a plot and the hit popup listens for hits. On the client, Workspace parts can be missing or streamed out, so:
- read state from **server-set attributes or ReplicatedStorage**, not from parts that may not exist;
- use `WaitForChild` with a timeout;
- handle `nil`.

**Every subagent gets these links** in its prompt, and reads the ones that apply to its job:

| Note | URL | Who needs it |
|---|---|---|
| Player psychology checklist | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/27%20-%20Checklists/Roblox%20Player%20Psychology%20Checklist.md | all |
| Player psychology guidelines | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/18%20-%20Game%20Design/Roblox%20Player%20Psychology%20Guidelines.md | all |
| UI checklist | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/27%20-%20Checklists/UI%20Checklist.md | anyone touching UI |
| Mobile UI checklist | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/27%20-%20Checklists/Mobile%20UI%20Checklist.md | anyone touching UI |
| UI engineering guide | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/07%20-%20UI%20UX/Roblox%20UI%20Engineering%20Guide.md | anyone touching UI |
| Notification queue recipe | https://github.com/Apollo9360/roll-egg-notes/blob/main/roblox-dev/28%20-%20Recipes/Recipe%20-%20Notification%20Queue.md | features 2, 3, 4 (toasts and popups) |

**Reference screenshots** are on the branch in `handoffs/2026-10-15-refs/`. They come from **other games**, so use them for look and layout only and copy no assets:

| File | What it shows |
|---|---|
| `playtime-rewards.webp` | A "PLAYTIME REWARDS" panel |
| `starter-packs.webp` | A "Starter Packs" panel |
| `dont-leave.webp` | A "don't leave" overlay around Roblox's exit modal |

They are described in text under each feature, in case you can't open images.

## 1. Facts about the game (from the 2026-10-14 Studio report, pass-free)

**Treat these as leads and re-check each one in Studio.**

- **Zones:** Meadow (start), Riverbank (opens at 300 Speed), Canyon (8,000 Speed). Zone data lives in `ReplicatedStorage.Shared.Config.Biomes`; check whether there are more zones.
- **First pet:** the first egg hatches a guaranteed Epic Owl worth about **$85/s**. Rebirth costs `250,000 × 3.5^rebirths`.
- **Pet income:** `$/s = BaseIncome × size^1.6 × mutation × Multipliers.Money` (`Curves.PetIncome`).
- **Egg reset:** eggs reset every 300 s, with 10 s of night.
- **Offline income:** `Economy.OfflineIncomeFraction` = 0.5, capped at `Economy.OfflineMaxSeconds` = 8 h. Offline $/hour = current income/s × 3600 × 0.5.
- **Hatching:** placed eggs hatch on a timer. `HatchT1–T4` are Robux Instant Hatch products (9 / 49 / 149 / 299 R$ for ≤2 min / 10 min / 30 min / any).
- **Speed packs:**
  - **SpeedL** (Speed Pack L) is the largest: 349 R$, product id `3718311203`.
  - Its effect is +max(400, zone span × 200%) Speed.
  - S and M cost 49 and 129 R$.
- **Other products:**
  - Starter2xSpeed: 3 R$, once.
  - Pass2xMoney: 399 R$. Pass2xGrowth: 467 R$.
  - Luck15 / 30 / 60: 49 / 99 / 199 R$.
  - Money30: 99 R$.
  - Every Robux button opens an in-modal confirm first ("Buy X for R$ N?").
- **Tutorial:** `Config.Tutorial`, `TutorialService.setStep`, `TutorialController`. Step indices are saved and append-only.
- **Code layout:**
  - Server: `ServerScriptService.Server.Services`: EggService, GuardService, SpeedService, RetentionService, TutorialService, WorldService, LeaderboardService, OnboardingService.
  - Client: `StarterPlayer.StarterPlayerScripts.Client.Controllers`: HudController, TutorialController, CarryController, EggFxController, SpeedFxController, FeelController, GuardFxController.
- **Existing UI you were told about:**
  - `game.StarterGui.OnHitPromptBuy`, the base for feature 4;
  - something named **`RunRed`**, probably the red "RUN!!" chase overlay. Search for it.
- **Paid random items policy** (official: https://create.roblox.com/docs/production/monetization/paid-random-items): if Robux buys a random outcome (e.g. a "Skip All" that gives an egg which hatches randomly), show every outcome and its numeric odds **before purchase**. Free rewards need no odds.

## 2. The five features

### Feature 1: a high-zone freebie egg during the tutorial

**What the user wants:** at some point in the tutorial, the player gets a free egg from a high zone with a hatch timer of about 15 minutes.

**Why it works:** it's a gift before any ask (reciprocity), and an unfinished task that keeps them playing or brings them back (Zeigarnik effect, appointment). It also previews a zone they can't reach yet.

**Spec:**
- **Grant point:** recommend right after the first hatch (the player has just learned what hatching is). The user may pick another step.
- **Grant once per account, server-side.** Save a flag so it can't be granted again (rejoin, tutorial reset, step replays).
- **Zone:** the highest-value zone whose egg the player can't reach yet. Read `Config.Biomes` for the list.
- **Where it goes:** check whether it takes a pen or incubator slot. It must not block the tutorial's own egg steps. If slots are tight, give it its own spot on the plot.
- **Timer:** about 15 min (900 s), owned by the server, in real time. Recommend it **keeps counting while the player is offline**, so they come back to a ready egg.
- **Display:** a countdown above the egg, plus a "your <Zone> egg hatches in 14:59" line in the HUD or Next Goal area after the tutorial.
- **Instant Hatch:** it may be offered on this egg like on any placed egg. It skips time, not the outcome, so it is not a paid random item. Confirm in code that Instant Hatch doesn't change the result.
- **Outcome:** rolls from that zone's normal pet table. A floor (e.g. Rare or better) is a decision for the user.
- **Hatch moment:** a bigger celebration than a normal hatch (peak-end rule).
- **Analytics:** add a funnel or custom event for "freebie granted" and "freebie hatched". Check the `AnalyticsService` signatures first.

### Feature 2: playtime rewards, 12 gifts, rolling 24 h

**Reference (`playtime-rewards.webp`):**
- A wide panel titled "PLAYTIME REWARDS" with a "3/12 CLAIMED" pill.
- A 4×3 grid of tiles. Claimed tiles are grey with a green check and "CLAIMED".
- Unclaimed tiles are coloured by reward type:
  - green for money ("+$66.6K");
  - purple for potions/boosts ("x2 WALK SPEED 15 MIN", "x5 CASH 15 MIN");
  - orange for trails;
  - pink and teal for eggs and items.
- Each tile has a big icon and a dark countdown pill ("1:30", "11:30", "1H 53M").
- **Side column:**
  - a gift-box-and-clock icon;
  - "Your time is saved! New gifts in 20h 38m";
  - a blue "SKIP ALL" button with a Robux price and a small purple "ODDS" tag;
  - a "CLAIM ALL" button.
- **HUD:** a "Playtime" button with a live countdown under it ("1:43").

**Spec:**
- **12 gifts at cumulative playtime thresholds, weighted early.** Recommended, in minutes: **1, 2, 3, 5, 7, 10, 13, 17, 22, 30, 40, 60**. Half of the gifts land in the first 10 minutes.
- **Rolling 24 h cycle:**
  - The cycle starts when the player first earns playtime in a new cycle.
  - Playtime in the cycle is **saved across sessions** ("Your time is saved!").
  - **24 h after the cycle started**, it resets: thresholds, claims and time all go back to zero. The panel shows "New gifts in Xh Ym".
  - Use server time (`os.time()` / `workspace:GetServerTimeNow()`), not client time.
- **Server-authoritative:**
  - the server counts playtime (while the player is in-game);
  - the server validates every claim (threshold reached, not already claimed, cycle id matches);
  - the server grants the reward.
  - Claims go through one validated remote, rate-limited.
- **Rewards look strong and rise in value:**
  - Early tiles: money, Speed, a lower-zone egg, a short x2 boost.
  - Later tiles: trails, x5 boosts, eggs from higher zones.
  - The 60-minute tile: the best gift (e.g. a high-zone egg or an exclusive trail).
  - Cash and Speed amounts may scale with the player's income (like the reference's "+$133K").
  - Big icons and rarity colours: each colour means one reward type, used the same way everywhere.
- **Claim flow:**
  - A ready tile pulses.
  - The HUD Playtime button shows a badge and a countdown to the next gift.
  - A toast comes through the notification queue recipe ("Gift ready! Open Playtime").
  - "CLAIM ALL" claims every ready tile.
- **Skip All (Robux):** **ask the user** whether to include it. If yes:
  - it is a developer product that unlocks all remaining tiles for this cycle;
  - the user creates it in Creator Hub, and you list it for them;
  - if any remaining tile gives a random outcome (an egg), the **ODDS** button must show those tiles' pet tables as percentages **before** purchase.
- **Ethics:** no gift is lost if the player leaves. Reaching a threshold offline isn't required; time simply pauses.

### Feature 3: three cheap starter packs

**Reference (`starter-packs.webp`):** a "Starter Packs" panel with "0/3 Owned" and three columns, each with a big round icon, a list of contents and a green price button.

| Column | Colour | Contents | Price |
|---|---|---|---|
| STARTER PACK | Blue | x5 Money potion 15 min, x5 Speed potion 15 min, an exclusive pet with a fixed income | 9 R$ |
| SUPER STARTER PACK | Purple | An exclusive pet with a large fixed income, an exclusive trail ("x4 Money x12 Speed") | 59 R$ |
| MEGA STARTER PACK | Gold | An exclusive pet with a huge fixed income, a top trail ("x6 Money x15 Speed"), "x2 Money Forever" | 199 R$ |

The HUD also shows a corner offer, "STARTER PACK! ONLY 9 R$".

**Spec:**
- **Three developer products, each purchasable once per account.** Save the ownership; "x/3 Owned" reads from saved data.
- **Grant via the existing receipt handler.** It must grant exactly once, survive a rejoin retry, and return `PurchaseGranted` only after saving.
- **Contents mix temporary and permanent rewards:**
  - temporary boosts (15 min x5 potions);
  - strong permanent items that **don't scale with progress**: exclusive pets with a **fixed $/s** that ignores size, mutation and luck, and exclusive trails with fixed multipliers.
  - Strong early, a nice extra late.
- **Numbers:** propose them in the decision message, sized against this economy:
  - Starter pet ≈ 5–10× the first Owl ($85/s);
  - Super pet ≈ a strong Riverbank pet;
  - Mega pet ≈ a top Canyon pet.
  Check the real income tables in config before proposing.
- **Decided by the user (2026-10-15):**
  - "x2 Money Forever" from the Mega pack **stacks** with `Pass2xMoney`: an owner of both earns ×4 from these two. Make it a separate saved multiplier, and show the stacking in the pack text.
  - **Scrap the old `Starter2xSpeed` (3 R$) product.** Remove its HUD offer and Shop entry and its config entry. Keep its receipt handler able to grant it, so any purchase still in flight is honoured, and note this in DEV_NOTES. Tell the user to take it off sale in Creator Hub (don't do it yourself).
- **When to show it:**
  - **not before the first reward lands, and not during the tutorial** (the psychology rule is to give before you ask);
  - recommended: the HUD corner offer and the panel become available after tutorial completion;
  - one automatic popup at most per session after that.
- **Look:** follow the reference layout, with big round icons and a clear contents list.
- **Creator Hub:** list the three products (name, price, description, icon idea) for the user. Don't create them yourself.

### Feature 4: on-hit popup for the largest speed pack

**Start from `game.StarterGui.OnHitPromptBuy`.** Inspect it first and keep what exists.

**Spec:**
- **Trigger:** "whenever you get hit". Find every server-side event where a player is hit:
  - a guard catch;
  - a trap;
  - another player knocking them or their egg loose (the quest kind `KnockLoose` suggests this exists).
  Fire one client event per hit. If it's unclear which count as "hit", list them and recommend all.
- **The popup slides down from the top of the screen,** offering **SpeedL (Speed Pack L, 349 R$)**:
  - show what it gives in numbers for this player (e.g. "+2,400 Speed"), computed by the server from the pack formula;
  - include a close button;
  - auto-hide after a few seconds.
- **Buy button:** opens the game's normal in-modal confirm, then `MarketplaceService:PromptProductPurchase`. The grant goes through the existing receipt handler.
- **Guards against spam:**
  - don't stack: if it's already showing, refresh the timer instead of opening a second one;
  - don't show during the tutorial or the first-timer grace window;
  - **cooldown: 3 minutes between shows** (decided by the user, 2026-10-15). Hits during the cooldown show nothing.

### Feature 5: "don't leave" overlay when the player tries to leave

**Reference (`dont-leave.webp`):**
- Roblox's own centre modal ("New to the Roblox App… Close Roblox / Back to Home") sits in the middle of a dimmed screen.
- Around it, outside its rectangle, the game shows:
  - **top:** "WAIT! DON'T LEAVE YET!" in big yellow outlined text, with "You earn **$162.7K/hour** offline!" (the amount in green);
  - **middle band, left and right of the modal:** "Your eggs keep … while you're away!";
  - **bottom:** "You have 2 eggs READY to hatch!".
- The red "RunRed" style vignette pulses around the edges.

**How to detect "trying to leave" (research all kinds and test each):**
- `GuiService.MenuOpened` / `GuiService.MenuClosed` / `GuiService.MenuIsOpen`: the Roblox menu opened, by Esc, the Roblox button or gamepad Start. The leave flow goes through it.
- `UserInputService.WindowFocusReleased` / `WindowFocused`: the window lost focus (minimise, alt-tab; check what it does on mobile backgrounding).
- Search the current Engine API reference for any other leave or exit signal. Document what each one covers on PC, mobile, console and VR.
- `Players.PlayerRemoving` fires on the server **after** they've left, so it's useless for showing UI.
- Write the coverage table (signal × device) into the report.

**Spec:**
- **Show** on any of those signals. **Hide** on `MenuClosed` / `WindowFocused`.
- **Pulse `RunRed`:** find it, reuse it, and don't build a second red overlay.
- **Text** (exact numbers from the server, replicated as attributes):
  - **Line 1:** "WAIT! DON'T LEAVE YET!"
  - **Line 2:** "You earn $X/hour offline!", where X = current income/s × 3600 × `OfflineIncomeFraction`. If the 8 h cap matters, mention it honestly.
  - **Line 3, the reason, in priority order:**
    1. if any placed eggs are ready: "You have N eggs READY to hatch!";
    2. else if any are hatching: "N eggs are hatching! Next one in m:ss";
    3. else: another true reason, e.g. the next playtime gift ("Free gift in m:ss") or the freebie egg's timer. Never invent a reason.
- **Placement:** Roblox's menu and modal draw **above** game GUIs, so place every element **outside the central modal area**: top band, bottom band, and the sides of the middle band. Check it at several resolutions, including a small phone (portrait and landscape) and a 4:3 tablet.
  - Use a high `DisplayOrder`.
  - Check `ScreenInsets` / `IgnoreGuiInset` so the top band isn't pushed under the top bar.
- **Honest:** no fake numbers, no blocking the leave, no repeated nagging. It shows while the menu is open, and that's all.

## 3. Method (director)

### Step A: fact base (read-only), in parallel

Launch **five subagents at once**, one per feature. They only read: `script_read` / `script_grep` / `search_game_tree` / `inspect_instance`, and `loadstring(Source)` for compile checks.

**Prompt template:**

```text
You are auditing Roll and Steal an Egg! in Roblox Studio for ONE planned feature. READ-ONLY: no edits,
no playtests, no require() of server modules. Studio: list_roblox_studios → place 122907972658396.
Feature: <PASTE THE FEATURE SECTION FROM THE HANDOFF>.
Game facts (verify, don't trust): <PASTE SECTION 1>.
Read these first (fetch the URLs): <PASTE THE LINKS FROM SECTION 0 THAT APPLY>.
Return:
1. Existing systems this feature must reuse (exact paths, line numbers, the functions to call).
2. Data: where player data lives, the schema/version field, how new fields get defaults.
3. Remotes: where they're registered and how they're validated; the receipt handler path.
4. UI: existing instances to extend (paths), style to match (fonts, colours, strokes).
5. Streaming risks for this feature (parts that may be missing on the client).
6. Questions only the user can answer, each with your recommendation.
7. A build plan: ordered steps with files, server vs client, effort XS–L.
Quote code; say "not found" with where you looked rather than guessing.
```

**Then run one skeptic subagent over all five audits:** "Re-check every path, line number and claim from the code yourself. Mark each CONFIRMED or WRONG, with evidence." Fix the fact base, and write it to `handoffs/2026-10-15-retention-monetization-audit.md`.

### Step B: one decision message to the user

Ask in **one message**, with a recommendation for each:

| # | Question | Recommendation |
|---|---|---|
| Q1 | Tutorial step for the freebie egg | Right after the first hatch |
| Q2 | Freebie: which zone, any rarity floor, does the timer run offline? | Highest zone not yet unlocked; Rare+ floor; runs offline |
| Q3 | Playtime thresholds and the 12 rewards (show your proposed list) | The 1 … 60 min list above, best gift at 60 |
| Q4 | Include Skip All (Robux)? Price? | Yes, 199 R$, with an ODDS button |
| Q5 | Starter pack prices and contents (show the numbers) | 9 / 59 / 199 R$ as in the reference |
| Q6 | What counts as "hit" for the popup (list what you found) | All hit sources; never in the tutorial |

**Already decided (don't ask again):**
- the Mega pack's x2 Money **stacks** with the pass;
- **scrap** the 3 R$ `Starter2xSpeed`;
- the hit popup has a **3-minute cooldown**.

Also include the Creator Hub list of products the user must create, with names, prices and descriptions.

### Step C: build

**Order:**
1. Feature 4 (smallest; existing UI).
2. Feature 5.
3. Feature 1.
4. Feature 2.
5. Feature 3 (needs the user's product ids). Build it with placeholder ids in config, behind a flag that stays off until real ids are set.

**Build one feature at a time.** Parallel builders editing the same place can overwrite each other. A builder subagent may do one feature while you review the previous one, but only if their scripts and instances don't overlap.

**For each feature:**
1. Reuse what the audit found. No second clock, shop, data path or notification system.
2. **Server-authoritative:** the server owns timers, claims, grants, ownership, counts and prices. The client only shows them.
3. **Data is migration-safe:** new fields get defaults, and old saves still load.
4. **UI is built as real instances** in StarterGui; controllers only bind. Run the UI checklist and the Mobile UI checklist against every new screen.
5. Run a compile check (`loadstring(Source)` via `execute_luau` in Edit mode) and take a `screen_capture` of every new UI.
6. **Run three critic subagents, in parallel, on the finished feature:**
   - **UI critic:** screenshots against the UI and Mobile UI checklists, and the reference image;
   - **security and data critic:** remotes, receipts, saves (use the `roblox-dev:roblox-reviewer` agent if it is already installed);
   - **psychology critic:** the psychology checklist and its ethics gate.
   Fix what they confirm.
7. Add a `DEV_NOTES.md` entry, newest first, under `## Changelog`.

## 4. Rules (from the user; mandatory)

- **NEVER download or install anything as a skill** (or plugin) unless the user explicitly said so beforehand. Use only skills and tools that are already installed.
- **No playtests without the user's permission.** The user playtests and reports.
- **Before anything visual,** load the user's installed skill `roll-egg-art-direction` (`~/.claude/skills/roll-egg-art-direction/SKILL.md`) and follow it.
- **Never move or rebuild the map.** Inspect before editing; don't override the user's changes.
- **Never copy** from `ServerStorage.REFERENCE_PARKED`, `REFERENCE_PLEASEUSE`, `Quarantine`, `Snapshots_*`, `W1_Backups` or the Steal an Egg decomp.
- **Never copy** assets from the reference screenshots' games.
- **No Creator Hub.** List new products for the user; never click through it or enter credentials.
- **Edit-mode `require` caches stale modules.** Verify with `loadstring(Source)` or a clone parented next to its siblings.
- **Ethics** (the psychology checklist's gate):
  - no fake numbers or timers;
  - nothing blocks leaving;
  - offers never appear before the player has been given something;
  - every Robux button keeps its confirm step.

## 5. Stop points and finish

1. **After Step A:** send the Step B message and wait. Build only answered items. If something is unanswered, skip it and keep going.
2. **When done:**
   - Commit on `claude/stoic-lamport-jqadwi`:
     - the audit file;
     - a report at `handoffs/2026-10-15-retention-monetization-report.md`;
     - screenshots in `handoffs/2026-10-15-refs/built/`.
   - Tell the user it's ready to push.
3. **The report covers:**
   - the decisions made;
   - what was built (paths);
   - the leave-signal coverage table;
   - compile results;
   - critic findings and fixes;
   - the Creator Hub list;
   - a playtest checklist for the user, covering:
     - a fresh-save tutorial run (the freebie egg);
     - a playtime cycle check (use a debug time offset if one exists; otherwise say how to test the 24 h reset);
     - a purchase pass on each pack and on SpeedL;
     - getting hit;
     - opening the Roblox menu on PC, a phone (Device Simulator) and a gamepad.
