---
tags: [huds-guide, technical]
---

# UI scaling and layout - Layout fixes

Up: [[UI scaling and layout]] · Previous: [[UI scaling and layout - UIStroke]]

| Problem | Fix | Confidence |
|---|---|---|
| ScrollingFrame setup | CanvasSize 0, AutomaticCanvasSize on the needed axis, plus UIListLayout or UIGridLayout | High |
| AutomaticCanvasSize cuts off the last item | Convert children to Offset, resize canvas, convert back to Scale | Medium |
| ScrollingFrame clips UIStroke | Add UIPadding; or a holder frame with the ScrollingFrame at ~0.975 Y | Medium |
| Can't scroll to the bottom | UIPadding plus an invisible filler frame (not for UIGridLayout); or enlarge CanvasSize | Medium |
| Newest item at top | UIListLayout.SortOrder = LayoutOrder; set each LayoutOrder | High |
| Buttons spread apart on other devices | UIListLayout VerticalFlex was SpaceBetween; set None | Medium |
| Two-column grid | FillDirection Horizontal, FillDirectionMaxCells 2, HorizontalAlignment Left | Medium |
| Grid items wrong size | No UIAspectRatioConstraint on children; use Scale CellSize and multiply X and Y | Medium |
| UI changes shape on resize | Offset somewhere in the tree; convert everything to Scale | High |
| Mobile UI huge for one player | Their Roblox text size setting is large; lock text scale | Medium |
| Text vanishes when enlarged | TextSizeConstraint with a max, or enlarge the label | Medium |
| Looks fine in Studio, broken in playtest or live | Scale properly (Kekui); "+1"-style UIs break most often | High |
| Players miss "press 1 to equip" | The hotbar is drawn under other UI; raise its ZIndex or move it | Low |

- When players report broken UI, ask their phone model and orientation, and test on 1080p and mobile.
