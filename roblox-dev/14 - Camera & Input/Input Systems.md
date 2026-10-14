---
title: Input Systems
category: Camera & Input
tags: [input, input-action-system, contextactionservice, userinputservice, gamepad, touch, cross-platform, rebinding]
status: active
source_quality: AUTHORITATIVE
last_verified: 2026-10-14
aliases: [Input Action System, InputAction, ContextActionService, Cross-platform input, Gamepad support, Mobile controls, Key rebinding]
---

# Input Systems

## Summary

Define **actions** (Jump, Sprint, Interact, Fire), not keys, and bind each action to keyboard/mouse, gamepad **and** touch. Roblox's modern way is the **Input Action System**: `InputContext` → `InputAction` → `InputBinding` instances, authored in Studio. It is required for the Server Authority model.

Detect the player's **preferred input** with `UserInputService.PreferredInput` and adapt UI hints to it.

## API choice

| Need | Use |
|---|---|
| Cross-platform gameplay actions, context switching (play / menu / vehicle), server authority | **Input Action System**: `InputContext` (enable or disable, `Priority`, `Sink`), `InputAction` (`Type`: Bool, Direction1D/2D/3D, ViewportPosition; events `Pressed`, `Released`, `StateChanged`), `InputBinding` (`KeyCode`, composite Up/Down/Left/Right, `Scale`, `UIButton` for touch) |
| Older code, or quick bind/unbind with auto touch buttons | `ContextActionService:BindAction(name, fn, createTouchButton, ...inputs)` / `UnbindAction`; return `Sink` or `Pass` |
| Raw device events and state (mouse position, typing focus, low-level) | `UserInputService` (`InputBegan`, `InputChanged`, `InputEnded`; check `gameProcessedEvent` so typing in chat doesn't fire actions) |
| Primary input type for UI | `UserInputService.PreferredInput`, and its change signal |
| UI buttons | `GuiButton.Activated` (works for mouse, touch and gamepad selection) |

Beta as of 2026-10: `InputActionLabel` (shows the right key or button glyph) and the **Input Action Manager** tool. Enable them via File → Beta Features.

### Input Action System setup

From the docs:
1. Make a `ReplicatedStorage/Inputs` folder with an `InputContext` (e.g. `PlayContext`). Optionally set `Priority` 2000 and `Sink` on so it consumes bound keys.
2. Add `InputAction`s, e.g. `CharacterSprint` of type Bool, or `CameraRotate` of type Direction2D.
3. Under each action, add one `InputBinding` per device:
   - keyboard: `LeftShift`, or composite WASD;
   - gamepad: `ButtonY` or `Thumbstick2`;
   - mouse: `MouseDelta` with `Scale` 0.01;
   - touch: link `UIButton` to an on-screen button, or `TouchDelta`.
4. Set `Workspace.PlayerScriptsUseInputActionSystem` so the default player scripts use it.
5. For server-authoritative games, clone contexts **under each Player** from a server script, so the engine knows who owns them.

## Cross-platform rules

These come from Roblox's adaptive design guidelines and input docs:
- **Every action works on gamepad, keyboard/mouse and touch.** Menus must be focusable with a D-pad or stick.
- **Hints match the active input.** "Press A", "Tap", "E". Swap them when `PreferredInput` changes.
- **`TouchEnabled` isn't "mobile".** Touch laptops exist, and phones can have gamepads.
- **`GetLastInputType` thrashes** (MouseMovement ↔ Keyboard). Use it only for advanced cases.
- **Touch:**
  - large buttons in thumb reach;
  - avoid the top-bar and notch area (safe insets);
  - don't stack buttons too close together;
  - tap vs hold must be obvious.
- **Hold vs tap:** give holds a visual fill. ProximityPrompt `HoldDuration` does this for you. **Validate hold time on the server** for anything valuable ([[Remote Security and Anti-Exploit]]).
- **Rebinding:** with the Input Action System, change `InputBinding.KeyCode` at runtime, persist the player's choice in their profile, and re-apply it on join.
- **Drag:** `UIDragDetector` for UI and `DragDetector` for 3D, both built in. Server-side drag events still need validation.

## Default bindings

Roblox reserves several default bindings, including movement, jump, camera and menu. See the docs' default-bindings table before claiming keys. Gamepad **A** usually means jump or accept, and **B** cancel or back (Roblox gamepad docs).

## Common mistakes

- Keyboard-only actions with no touch or gamepad path. That excludes most of the audience.
- Handling `InputBegan` without checking `gameProcessedEvent`, so actions fire while typing in chat.
- Touch buttons that cover the thumbstick or jump button.
- Using `UserInputService` events for core simulation in server-authority games, which breaks prediction.

## Related notes

- [[Camera Systems]]
- [[Roblox UI Engineering Guide]]
- [[Controller UI Checklist]]
- [[Network Ownership and Physics Security]] (server authority)

## Sources

All AUTHORITATIVE, from creator-docs, 2026-10-13:
- [Input overview](https://create.roblox.com/docs/input)
- [Input Action System](https://create.roblox.com/docs/input/input-action-system)
- [Gamepad input](https://create.roblox.com/docs/input/gamepad)
- [Mobile input](https://create.roblox.com/docs/input/mobile)
- [Adaptive design](https://create.roblox.com/docs/production/publishing/adaptive-design)

## Last Verified

2026-10-14
