---
tags: [huds-guide, technical]
---

# UI scaling and layout - Fundamentals

Up: [[UI scaling and layout]] · Next: [[UI scaling and layout - Conversion plugins]]

- Use Scale, not Offset, for Size and Position. Scale is proportional to screen; Offset is fixed pixels.
- Offset-to-scale plugins, aspect-ratio constraints and correct anchor points fix ~99% of UI issues.
- Start frames at {1,0},{1,0} and use AnchorPoint 0.5,0.5.
- Add UIAspectRatioConstraint (1 for squares), or set a button's SizeConstraint to RelativeXX or RelativeYY.
- Turn on IgnoreGuiInset on the main ScreenGui.
- Use TextScaled, and ImageLabel ScaleType Fit instead of Stretch.
- Build and check in both mobile and PC views, and confirm properties read Scale (1,0) rather than Offset (0,100).
- UIListLayout padding should also use Scale.
- Use one ScreenGui per feature (Shop, HUD, customization) and toggle Enabled, with a parent frame per screen.
- Don't put a menu's open button inside that menu's ScreenGui.
- Group related buttons in one parent frame (e.g. a left-side frame) with a UIAspectRatioConstraint and a UIListLayout.
- Wrap mobile vs PC layouts in an invisible container with a UIAspectRatioConstraint.
- For the iPhone notch, put UI in a separate ScreenGui that respects DeviceSafeInsets.
- For a full-screen ImageLabel, use Scale and check the ScreenGui's ScreenInsets.
