---
title: UI Checklist
category: Checklists
tags: [checklist, ui]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [UI review]
---

# UI Checklist

- [ ] Authored in StarterGui (or components). Controllers only bind and render.
- [ ] Scale-based layout with AnchorPoint; aspect-ratio constraints on icons and shapes.
- [ ] `ScreenInsets = CoreUISafeInsets` on interactive ScreenGuis; nothing important under the top bar or notch.
- [ ] `ResetOnSpawn = false` on stateful UI; DisplayOrder layering set.
- [ ] Text: readable at the smallest size (MinTextSize ≥ 9); body text grows with Preferred Text Size (no TextScaled on body).
- [ ] Every button has hover, press, selected and disabled states, plus a click sound on `Activated`.
- [ ] Every action gives feedback: pending → success or failure **with a reason**.
- [ ] Gamepad: Selectable set; SelectedObject set on open and restored on close; B closes modals.
- [ ] Reduce Motion respected; PreferredTransparency applied to backgrounds.
- [ ] Soft and premium currencies visually distinct; consistent close button, corners and strokes (design tokens).
- [ ] No per-frame UI updates without real animation; long lists pooled; ViewportFrames minimal.
- [ ] No rotated containers that need clipping.
- [ ] Localization-ready: no text in images, AutomaticSize, AutoLocalize off for names.

Details: [[Roblox UI Engineering Guide]] · [[UI Visual Design and Polish]]

## Sources

[Roblox UI docs](https://create.roblox.com/docs/ui), [Accessibility](https://create.roblox.com/docs/production/publishing/accessibility). AUTHORITATIVE.

## Last Verified

2026-10-14
