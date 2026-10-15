=== 01.md
## 1. First 60 seconds and tutorial (7/10)
1. Auto-place the starter Owl in the pen at hatch (PenService.hatch, `egg.Starter`), so no hotbar step and first purchase about 6 s sooner. Effort S.
2. Guarantee step 1 can be completed: a starter nest that respawns every 10 s for `EggsDelivered == 0` players, and match plots to the server cap. Effort M.
3. Rewrite the card for phones (6 words, 16 px, no E/F letters, no n/16), hide Shop/Daily/Quests/Rebirth until step 8, and end the tutorial with a Riverbank beam plus a "+320 SPEED / Riverbank unlocked" moment. Effort S.
=== 02.md
## 2. Core loop: steal, roll, escape (6/10)
1. Respawn nest eggs every ~20 s with a visible "next egg" timer (and a fast starter nest for tutorial steps 1 and 11), so the loop cannot dead-end for ad-driven newcomers.
2. Make the Meadow hen a fair threat after the first delivery (WalkSpeed 13 to 17) and fix the false "Kite the Mother Hen" text with "Run to the gold line!".
3. Add cause-specific failure and gate messages ("Croc is too fast! Need Speed 300") plus a carry-card $/s preview, so every failed or successful run teaches the next step.
=== 03.md
## 3. Egg growth, luck and the hatch (6/10)
1. Turn the clover into odds ("Epic+ 27%") on the carry card and egg tag, and teach "bigger egg = luckier pet" in tutorial step 3. XS-S.
2. Make the hatch reveal scale with rarity (longer shake, flash, FOV punch, pet icon on the card, NEW! reward line). M.
3. Fix the early pet pipeline: starter gets a HUGE tier with its mutation kept, and the first three random eggs get an Epic-leaning luck floor; retune `SizeTiers` to 0.80/0.93/0.99. S.
=== 04.md
## 4. Economy and pacing (6/10)
1. Give new players a personal starter nest egg and respawn emptied nests every 20-30 s (not once per 300 s), because 5 eggs per zone per 5 minutes is shared by the whole server. Effort S-M · Impact Very high.
2. Start with $100, put "buy the wheel" first, and make the second tutorial egg a Riverbank egg with a 60-90 s timer to remove the flat $92-$187/s stretch from 0:35 to 5:00. Effort XS-S · Impact High.
3. Rescale the late game: shorter jackpot hatch timers (HatchExponent 0.8 to 0.55, Mythic base time halved), more wheel/pen tiers (T7-T9, L7-L9), and Rebirth at 50M x 8^n, so something is still worth buying after minute 69. Effort M · Impact High.
=== 05.md
## 5. Progression systems (6/10)
1. Add a persistent "Next:" HUD pill ($ / $ bar, tap to get a beam) that cycles wheel, pen, trail, then next zone gate, then rebirth (Very high, S/M).
2. Make zone gates visible: move the EntranceSigns above ground (currently y = -84) and add an "UNLOCKED" banner when Speed crosses a gate (Very high, S).
3. Add an "Upgrades" panel with Buy buttons so cash upgrades do not require a run home; end the tutorial on Wheel 2 instead of a $5,000 trail (High, M).
=== 06.md
## 6. Zones, guards and the world as a challenge (5/10)
1. Raise all six EntranceSigns from Y=-84 to ~Y+16 and add an "Entering <Zone> - Needs N Speed (you: M)" banner plus a HUD "Next zone" goal pill after the tutorial.
2. Stop night from deleting carried eggs (return to the same nest slot, refill only empty nests) and add a 15-second "NIGHT IN 15 - GET HOME!" warning for carriers; fix the false "rolled back to its nest" text.
3. Make zone 2 mechanically new (stream crossing with bridges + slow water) and fix the dead Croc Lunge (`Range` 10→18) so each zone teaches one new thing instead of just being longer.
=== 07.md
## 7. Monetisation (5/10)
1. Add odds disclosure and a `PolicyService` gate to the three Luck products (Curves.Odds is unused; Golden Goose 1.0 to 8.5% is the number to show), because it is a policy breach on a product that sells.
2. Hide the Skip and Trail Robux prompts until `TutorialStep == 17`, and add a one-time 49 R$ Starter Pack shown at tutorial end.
3. Make paid timers pause while offline (or say clearly that they don't), and retry the pass check so a 399 or 467 R$ pass can never silently fail.
=== 08.md
## 8. Retention and return loops (5/10)
1. Add a free 8-hour "sleepy" egg and an always-on "Daily in HH:MM:SS" HUD chip at tutorial complete, so Day 1 ends with a reason to return (S, Very high).
2. Replace the UTC calendar-day streak with a rolling 20 h / 44 h window plus one free grace day, and make Day 3/5/7 pay visible pets or eggs, not just numbers (S-M, High).
3. Turn the offline toast into a "While you were away: +$X (8 h, FULL)" count-up card with a "Spend it" button, and make newbie protection count played seconds so Day 2 isn't a bat hit (S, High).
=== 09.md
## 9. Social and co-play (4/10)
1. Fix the 5-plot / 60-player gap: set the cap to 5 now, then build 8 plots with a waiting list (Very high).
2. Make Meadow nest eggs respawn in 20 s, or give tutorial kids a personal starter egg (Very high).
3. Add one post-first-hatch "Bring a friend" invite with a shared reward, and make the friend boost help Speed and growth (High).
=== 10.md
## 10. HUD, UI clarity and mobile (5/10)
1. Add a persistent "NEXT: <thing> $x / $y" chip (reuse the Tutorial card slot) so the goal never leaves the screen after step 16.
2. Gate the side buttons by tutorial step (hide Shop/Daily/Quests/Rebirth/Pets/Index until needed) and make the remaining ones at least 44 px high with 14 px minimum text.
3. Rewrite the phone tutorial card (14 px body, at most 10 words, no Settings overlap) and replace keyboard letters with a `{Hold}` token.
=== 11.md
## 11. World, first impression and performance (6/10)
1. Turn ReducedFx on automatically for touch-only devices and set CastShadow=false on all decor parts (3,812 casters now, target 800).
2. Set StreamingMinRadius 96 and StreamingTargetRadius 350 (74% of parts are within 1,024 studs today), and make WaterA non-persistent with its 500/s emitter cut.
3. Cap the server at 5 players or add overflow handling (only 5 plots exist), and spawn at the plot through `RespawnLocation` instead of the visible 15-stud teleport.
=== 12.md
## 12. Trust and security (6/10)
1. Set the server cap to the plot count (5) and show a toast when placement has no plot (`PlotService.assign`, `EggService.placeEgg`).
2. Add a two-tap sell confirm with a short lockout and protect the top pets (`HudController` lines 419-424).
3. Add a server movement check in `SpeedService.wheelTick` that snaps back illegal jumps and blocks grabs, placements and bat hits after one.
=== TOP3.md
=== 01.md
