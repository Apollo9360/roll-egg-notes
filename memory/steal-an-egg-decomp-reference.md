---
name: steal-an-egg-decomp-reference
description: "A Steal an Egg decomp is sometimes open in a second Studio (\"test\", placeId 123277966547265) as a visual reference"
metadata:
  node_type: memory
  type: reference
  originSessionId: d723eaa0-ed81-4b9b-bff2-dbdac7a03b61
  modified: 2026-10-13T01:36:52.262Z
---

The user opens a Steal an Egg decomp in a separate Studio instance named "test" (placeId 123277966547265). Use it as a look reference: screenshot it, read its MaterialService/UI structure. User allows toggling its ScreenGuis on/off for screenshots — restore the original Enabled/Visible states afterwards. Consult-only, same as REFERENCE_PARKED: don't copy its code/assets into our game.

Findings (2026-10-12): world is simple — vivid lime greens, "2022 Stud" MaterialVariant + faint checker Texture (rbxassetid://6905003203, 0.9 transparency, 8 studs) on walls, tall corridor walls. Pets/eggs are voxel models with Neon glow + particle auras/sparkles by rarity. HUD is panel-less heavy italic outlined text; menus are dark diamond-pattern panels with thick black outlines, bold white outlined titles, gradient header bars, square red X. Its Lighting values match ours. Full measured recipes live in the user-level skill `roll-egg-art-direction` (~/.claude/skills/roll-egg-art-direction/SKILL.md) — load it before any visual work on this game. Related: [[no-playtesting]].
