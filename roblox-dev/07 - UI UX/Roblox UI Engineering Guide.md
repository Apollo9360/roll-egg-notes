---
title: Roblox UI Engineering Guide
category: UI UX
tags: [ui, screengui, responsive, scale, anchorpoint, constraints, layouts, canvasgroup, styling, accessibility, gamepad]
status: active
source_quality: AUTHORITATIVE
last_verified: 2026-10-14
aliases: [Responsive Roblox UI, UI scaling, Safe areas, ScreenInsets, UI constraints, UI styling, StyleSheet, Mobile UI]
---

# Roblox UI Engineering Guide

## Summary

Build UI as **authored instances in StarterGui**; client controllers bind behaviour to them.

**Make it responsive:**
- Scale-based position and size with `AnchorPoint`.
- `UIAspectRatioConstraint` for shapes that must not stretch.
- `UISizeConstraint` and `UITextSizeConstraint` for limits.
- List, grid and flex layouts with `UIPadding`.

**Make it fit every device:**
- Respect safe areas (`ScreenInsets`).
- Adapt to the **preferred input** (touch, gamepad, mouse and keyboard).
- Honour the player's accessibility settings (text size, reduced motion, background transparency).

Use the **UI Styling** system (StyleSheets, tokens) for consistent design at scale.

## Containers and lifecycle

- **ScreenGui** in `StarterGui` is cloned into `PlayerGui` when the character first spawns. It is **not** cloned until `LoadCharacterAsync` if `CharacterAutoLoads` is off.
- **One ScreenGui per screen or layer** (HUD, Menus, Modal, Notifications). Toggle `Enabled`, and layer them with `DisplayOrder` (higher draws on top).
- **`ResetOnSpawn`:**
  - `true` (the default) deletes and re-clones the ScreenGui on every respawn, which breaks controller references and state.
  - Set it **false** for persistent UI.
- **`ScreenInsets`:**

  | Value | Keeps UI clear of | Use for |
  |---|---|---|
  | `CoreUISafeInsets` (default, recommended for interactive UI) | Top-bar buttons **and** notches | Buttons, HUD |
  | `DeviceSafeInsets` | Notches and cutouts, but **not** the top bar | Full-bleed layouts that avoid the top bar themselves |
  | `TopbarSafeInsets` | Area between the top-bar controls and the right safe edge; flexes with top-bar content | Top-bar-aligned widgets |
  | `None` | Nothing | **Non-interactive** backgrounds only |

- **UI is client-only.** Never put game logic in UI scripts; send intent to the server ([[Roblox Networking Best Practices]]).

## Position, size and responsiveness

- **`UDim2` = (scale, offset)** per axis:
  - **Scale** (a fraction of the parent) adapts to screen size;
  - **offset** (pixels) does not.
  - Use scale for layout. Use offset only for hairlines, strokes, or fixed-size touch targets guarded by constraints. See [[Decision - Scale vs Offset]].
- **`AnchorPoint`** sets which point of the element `Position` refers to. `(0.5, 0.5)` with position `(0.5, 0, 0.5, 0)` centres it; `(1, 1)` pins it bottom-right. AutomaticSize grows from the anchor.
- **`UIAspectRatioConstraint`** keeps a width-to-height ratio even with scale sizes. Use `1` for square icons and avatar thumbnails. **It overrides layouts.**
- **`UISizeConstraint`** sets min and max pixel size (e.g. buttons never smaller than a touch-friendly size). **It also overrides layouts.**
- **`UITextSizeConstraint`** sets min and max font size. Don't set `MinTextSize` below **9**; text becomes illegible.
- **`UIScale`** multiplies an element's AbsoluteSize, including strokes and corners. Use it for zoom and "pop" tweens without re-layout.
- **`AutomaticSize`** (X, Y or XY) grows a frame to fit its content, which suits localization and dynamic text. `Size` becomes the **minimum**. ScrollingFrames use `AutomaticCanvasSize`.
- **Layouts:**
  - `UIListLayout` for rows and columns, including flex behaviour with `UIFlexItem`;
  - `UIGridLayout` for inventories;
  - `UITableLayout`;
  - `UIPageLayout` for carousels;
  - `UIPadding` for consistent insets.

## Text

- **`TextScaled = true`** fits text to its box, but then the player's **Text Size** accessibility setting does *not* apply. For body text, prefer fixed sizes plus `AutomaticSize` and `TextWrapped`; these grow with the player's preference. Clamp with `UITextSizeConstraint` when `TextScaled` is needed (headlines, HUD numbers).
- Pick **one or two fonts** and a type scale, for example Title, Header, Body and Caption.
- **Rich text** (`RichText = true`) handles inline colour, bold and size. **Text filtering** is required for any player-written text shown to others (`TextService:FilterStringAsync`).
- Measure text with `TextService:GetTextSize` / `GetTextBoundsAsync`. Both honour the player's text-size preference.

## Visual modifiers

- `UICorner`, `UIStroke` (Contextual mode for text outlines, Border for frames), `UIGradient`.
- **9-slice** images (`ScaleType = Slice`, `SliceCenter`) stretch panels without distorting their corners.
- **`CanvasGroup`** renders its children as a single image, so `GroupTransparency` fades a whole panel uniformly. **It costs texture memory**, so use it for elements that animate or fade, not for everything.
- **Rotation and clipping:**
  - Without `StarterGui.ClipsDescendantsSupportsRotation`, any GuiObject with a non-zero `Rotation` **disables `ClipsDescendants` for itself and all descendants**.
  - With that setting enabled, rotated children are clipped correctly, but clipping *by* a rotated parent, or by `UICorner` rounding, is still not supported.
  - So: rotate leaf elements only, or put the rotation on a wrapper above the clipping frame.

## UI Styling (StyleSheets)

Roblox's CSS-like system, which drives the Style Editor:
- A **`StyleSheet`** holds **`StyleRule`s** with **selectors**:
  - class (`Frame`);
  - tag (`.ButtonPrimary`, via CollectionService tags);
  - name (`#Name`);
  - modifiers (`::UICorner`);
  - states (`:Hover`, matching `Enum.GuiState`);
  - queries (`@...`) for screen size, input type or accessibility.
- **Tokens** are attributes on a token StyleSheet: colours, sizes and fonts shared across rules. This is a real **design-token pipeline**.
- A **`StyleLink`** attaches one StyleSheet to a ScreenGui tree; only one sheet applies per tree.
- **In Studio**, styled properties show ⚠ in Properties, and overrides show in bold. "Reset to Default" returns to the styled value.
- **Use it** for game-wide consistency (button variants, panel themes, dark and light themes), and **use queries** for responsive and accessibility variants instead of scripts.

## Input-aware UI

- **Detect the primary input** with `UserInputService.PreferredInput`, and listen for changes.
  - `TouchEnabled` is **not** an "is mobile" check; touchscreen laptops exist.
  - `GetLastInputType` thrashes between values.
- Show the matching prompts ("Press **A**", "Tap", "**E**"). With the Input Action System, `InputAction.PreferredBinding` gives the right binding, and `InputActionLabel` (beta) displays it.
- **Gamepad navigation:**
  - mark interactive elements `Selectable`;
  - set `GuiService.SelectedObject` to the first control when a menu opens;
  - restore the previous selection when it closes;
  - group with `SelectionGroup` and `SelectionBehavior*`; override neighbours with `NextSelectionUp/Down/Left/Right`;
  - customize the highlight with `SelectionImageObject`.
  - Recipe: [[Recipe - UI Modal Manager and Button Feedback]].
- **Touch:** big targets. Keep core actions in thumb reach (bottom corners), and never put essential buttons under the top bar or notch.

## Accessibility settings to honour

| Player setting | API | What to do |
|---|---|---|
| Text Size | `GuiService.PreferredTextSize` | Use non-scaled text with AutomaticSize and wrapping so text grows |
| Reduce Motion | `GuiService.ReducedMotionEnabled` | Set tween time to 0; skip shakes, bounces and parallax |
| Background Transparency | `GuiService.PreferredTransparency` | Multiply your `BackgroundTransparency` by it |

More in [[Accessibility and Localization]].

## Performance

- **Fewer instances**: a flat hierarchy, and reuse templates. Clone list items from a template and **pool** them in long lists.
- **Don't update UI every frame** unless it is genuinely animating. Update on data change: attribute changed signals, or remotes.
- **Tweens are client-side** and cheap; dozens of simultaneous long tweens aren't.
- **ViewportFrames** (3D previews) are expensive. Keep few, small and static. Render on open, not continuously.
- **CanvasGroups** cost memory proportional to their pixel size.
- **Images:** most UI images need at most 512×512, small icons at most 256×256. Use sprite sheets (`ImageRectOffset`/`ImageRectSize`) for icon sets ([[Performance and Profiling Guide]]).

## Common mistakes

- Offset-only layouts that look right on one monitor and break on phones.
- `ResetOnSpawn` left `true` on stateful UI.
- Interactive UI with `ScreenInsets = None` ending up under the notch or top bar.
- `TextScaled` everywhere, so text ignores accessibility size and jumps between sizes across devices.
- Rotating a container that should clip its children.
- Showing keyboard hints to gamepad or touch players.
- No gamepad selection set when a menu opens, which strands console players.
- UI logic that grants rewards client-side.

## Related notes

- [[UI Visual Design and Polish]]
- [[UI Architecture Patterns]]
- [[Recipe - UI Modal Manager and Button Feedback]]
- [[UI Checklist]]
- [[Mobile UI Checklist]]
- [[Controller UI Checklist]]
- [[UI Development Context]]
- Project example: `skills/roll-egg-art-direction/SKILL.md` (measured recipes for one game's look)

## Sources

All AUTHORITATIVE, from creator-docs, 2026-10-13:
- [On-screen UI containers (ScreenInsets)](https://create.roblox.com/docs/ui/on-screen-containers)
- [Position and size](https://create.roblox.com/docs/ui/position-and-size)
- [Size modifiers and constraints](https://create.roblox.com/docs/ui/size-modifiers)
- [UI styling](https://create.roblox.com/docs/ui/styling)
- [GuiObject.ClipsDescendants (rotation behaviour)](https://create.roblox.com/docs/reference/engine/classes/GuiObject#ClipsDescendants)
- [Input overview (PreferredInput)](https://create.roblox.com/docs/input)
- [Input Action System](https://create.roblox.com/docs/input/input-action-system)
- [Accessibility](https://create.roblox.com/docs/production/publishing/accessibility)
- [Adaptive design guidelines](https://create.roblox.com/docs/production/publishing/adaptive-design)

## Last Verified

2026-10-14
