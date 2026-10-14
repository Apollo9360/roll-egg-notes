---
title: Decision - Scale vs Offset
category: Decision Guides
tags: [decision, ui]
status: active
source_quality: AUTHORITATIVE facts + labelled judgement
last_verified: 2026-10-14
aliases: [Scale vs Offset, UDim2]
---

# Decision - Scale vs Offset

| Use **Scale** (fraction of the parent) | Use **Offset** (pixels) |
|---|---|
| Layout position and size of panels, buttons, HUD groups | Strokes, hairlines, small fixed paddings |
| Anything that must adapt to the screen | Elements whose size must be exact, guarded by constraints |

**Combine:**
- **Scale + `UIAspectRatioConstraint`** keeps shapes from stretching;
- **Scale + `UISizeConstraint`** sets min and max pixel sizes (touch-target minimum);
- **`UITextSizeConstraint`** keeps text in a legible range (≥ 9).

**Anchor deliberately:** `AnchorPoint (0.5,0.5)` for centred elements, `(1,1)` for bottom-right HUD.

A **UIScale** on a root frame zooms an entire offset-built panel uniformly. Use it as a last resort for fixed-design panels.

See [[Roblox UI Engineering Guide]].

## Sources

[Position and size](https://create.roblox.com/docs/ui/position-and-size), [Size modifiers](https://create.roblox.com/docs/ui/size-modifiers). AUTHORITATIVE.

## Last Verified

2026-10-14
