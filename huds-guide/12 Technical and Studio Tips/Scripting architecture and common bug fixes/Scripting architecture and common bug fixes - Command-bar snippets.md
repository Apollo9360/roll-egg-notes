---
tags: [huds-guide, technical]
---

# Scripting architecture and common bug fixes - Command-bar snippets

Up: [[Scripting architecture and common bug fixes]] · Previous: [[Specific bugs and fixes]]

- Set AnchorPoint 0.5,0.5 on every GuiObject added during the session by connecting to DescendantAdded.
- Scale every BasePart by 1.2: loop over `game:GetDescendants()` and multiply each BasePart's Size.
