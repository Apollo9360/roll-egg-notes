---
tags: [huds-guide, technical]
---

# UI components, effects and polish - Effects recipes

Up: [[UI components, effects and polish]] · Next: [[UI components, effects and polish - Polish rules]]

- Tutorial spotlight: a transparent frame with a huge UIStroke (thickness ~99999999, transparency ~0.32). Add a thinner green stroke at higher ZIndex.
- The spotlight often stretches or bugs on mobile when centered. The 4-frame method is the fallback.
- Use a Beam for tutorial arrows; turn off FaceCamera if it shouldn't follow the camera.
- 3D buttons: a darker frame behind, offset down at lower ZIndex, or a darker base with a smaller frame inside.
- 3D text: a duplicate black TextLabel offset behind the original.
- UIGradient tints the base colour, so set the element white and pick colours in the gradient. Edit the sequence via "..." on Color.
- Gradient on text only: TextColor white and BackgroundTransparency 1.
- Shine or sunburst glitching with Rotation: put it in a CanvasGroup. CanvasGroup can also pixelate UI.
- "+1" popups: tween smaller as they fade, with random offset so they don't stack.
- Use a Spring module rather than TweenService for overshoot and bounce, e.g. a UIScale bounce on card reveal ([video](https://www.youtube.com/watch?v=lo33zHCCl5I)).
- Pop menus in and out rather than fading them; it feels more rewarding.
- Highlight outline look: FillColor black, FillTransparency 0, OutlineColor white.
- Highlight on one part only: clone the part, set it to Glass at 0.999 transparency, and put the Highlight on the clone.
- Inline icons use private-use unicode glyphs so outlines match text: `utf8.char(0xE002)` is Robux, 0xE001 is Premium, 0xE000 is Verified.
- UIShadow became available in late July 2026.

---
Related concepts: [[Bounce Rate]] · [[Onboarding and Tutorials]] · [[Genres and Saturation]] · [[Icons and Titles]]
