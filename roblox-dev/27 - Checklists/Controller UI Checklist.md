---
title: Controller UI Checklist
category: Checklists
tags: [checklist, ui, gamepad, console]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Gamepad checklist, Console UI]
---

# Controller UI Checklist

- [ ] Every interactive element is `Selectable`; navigation order is sensible (`NextSelection*`, `SelectionGroup`).
- [ ] Opening a menu sets `GuiService.SelectedObject`; closing it restores the previous selection.
- [ ] **B** closes the current modal; **A** activates.
- [ ] The selection highlight is obvious (`SelectionImageObject` styled to the game).
- [ ] Gamepad prompts show controller glyphs (`PreferredInput` / `InputAction.PreferredBinding`).
- [ ] All gameplay actions are bound to gamepad (Input Action System or ContextActionService).
- [ ] Tested with Controller Emulator **and** a real controller; checked against console publishing guidelines if shipping to console.
- [ ] No hover-only information; anything shown on hover is also shown on selection.

Details: [[Input Systems]] · [[Recipe - UI Modal Manager and Button Feedback]]

## Sources

[Gamepad input](https://create.roblox.com/docs/input/gamepad), [Console guidelines](https://create.roblox.com/docs/production/publishing/console-guidelines). AUTHORITATIVE.

## Last Verified

2026-10-14
