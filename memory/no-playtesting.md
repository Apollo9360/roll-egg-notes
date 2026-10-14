---
name: no-playtesting
description: User does all Roblox playtesting themselves; never start Studio play sessions
metadata:
  node_type: memory
  type: feedback
  originSessionId: 2982434e-2312-47a0-a3d2-2dcd8106c790
  modified: 2026-10-13T10:21:23.432Z
---

Don't start playtests (start_stop_play, character_navigation, user input sims) in Roblox Studio. The user playtests and reports issues.

**Why:** User said "Don't playtest. Let me playtest and report issues." and repeated "Do not playtest." on later requests.

**How to apply:** Verify only statically: compile-check sources with loadstring via execute_luau in Edit mode, inspect instances, read code. Say plainly in the report that nothing was playtested. `screen_capture` (with camera_position/look_at_position) is allowed and works while the user's play session is running — user said "You can screenshot during play mode." Use it to check visuals; read-only Server-datamodel queries also work then (Lighting props need pcall). Read-only Client-datamodel queries during the user's play session are great for live diagnosis (e.g. sampling a GUI property per frame found two writers fighting).

**Stopping play is OK:** script edits fail in Play mode ("Edit datamodel is not available in Play mode"). The user said (2026-10-13) I may stop their playtest (start_stop_play) to apply edits, as long as no subagents — mine or anyone else's — are running. Still never *start* one. Related: [[roblox-ui-in-startergui]].
