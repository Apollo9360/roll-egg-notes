---
name: roblox-ui-in-startergui
description: "Roblox menus/HUD must be real instances in StarterGui, not built purely in code; controllers only bind"
metadata:
  node_type: memory
  type: feedback
  originSessionId: 2982434e-2312-47a0-a3d2-2dcd8106c790
  modified: 2026-10-12T07:27:02.910Z
---

Build Roblox screen UI (HUD, menus, modals, row templates) as real instances in StarterGui; client controllers bind to them and clone hidden Templates folders for list rows/toasts. Don't construct ScreenGuis in code.

**Why:** The user wants to see and edit the UI in Studio. Same spirit as "builds must be real instances in Workspace, not script-only" ([[roblox-builds-in-workspace]]).

**How to apply:** Make the instances once with execute_luau, then rewrite controllers to WaitForChild them. World billboards attached to runtime objects can stay in code. For "Roll and Steal an Egg!" the layout follows Steal an Egg's screen positions. Also: Studio's screen_capture does not show ProximityPrompt UI even though it renders for the user, so don't debug prompt visibility from screenshots.
