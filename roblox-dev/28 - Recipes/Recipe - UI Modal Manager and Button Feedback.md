---
title: Recipe - UI Modal Manager and Button Feedback
category: Recipes
tags: [recipe, ui, modal, gamepad, buttons, tween, accessibility, luau]
status: active
source_quality: Built on AUTHORITATIVE APIs; code type-checked with luau-lsp
last_verified: 2026-10-14
aliases: [Modal manager, Tweened menu, Reusable button component, UI navigation, Button hover press]
---

# Recipe - UI Modal Manager and Button Feedback

## Summary

Two small client modules that make menus feel professional on every input:
- **ModalManager**:
  - one menu open at a time;
  - gamepad focus moves into the menu and back out when it closes;
  - gamepad **B** closes it.
- **ButtonFeedback**:
  - hover, press and gamepad-selection scale feedback through a `UIScale`;
  - tween time drops to zero when the player has **Reduce Motion** on.

## ModalManager

<!-- code: ReplicatedStorage/Client/UI/ModalManager.luau -->
```lua
--!strict
-- ReplicatedStorage/Client/UI/ModalManager (ModuleScript, client)
-- One modal open at a time: opening one closes the other, gamepad B closes it,
-- and gamepad selection moves into the modal and back out when it closes.
local ContextActionService = game:GetService("ContextActionService")
local GuiService = game:GetService("GuiService")

export type Modal = {
	gui: ScreenGui, -- each modal is its own ScreenGui (Enabled toggles it)
	firstSelection: GuiObject?, -- where gamepad focus goes when it opens
	onOpen: (() -> ())?,
	onClose: (() -> ())?,
}

local CLOSE_ACTION = "ModalManagerClose"

local modals: { [string]: Modal } = {}
local openName: string? = nil
local selectionBeforeOpen: GuiObject? = nil

local ModalManager = {}

function ModalManager.register(name: string, modal: Modal)
	assert(modals[name] == nil, `Modal {name} registered twice`)
	modal.gui.Enabled = false
	modals[name] = modal
end

function ModalManager.getOpen(): string?
	return openName
end

function ModalManager.close()
	local name = openName
	if name == nil then
		return
	end
	local modal = modals[name]
	openName = nil
	modal.gui.Enabled = false
	ContextActionService:UnbindAction(CLOSE_ACTION)
	-- Hand gamepad focus back to wherever it was before the modal opened.
	GuiService.SelectedObject = if selectionBeforeOpen and selectionBeforeOpen:IsDescendantOf(game)
		then selectionBeforeOpen
		else nil
	if modal.onClose then
		modal.onClose()
	end
end

function ModalManager.open(name: string)
	local modal = modals[name]
	assert(modal, `Unknown modal {name}`)
	if openName == name then
		return
	end
	ModalManager.close()
	selectionBeforeOpen = GuiService.SelectedObject
	openName = name
	modal.gui.Enabled = true
	if modal.firstSelection then
		GuiService.SelectedObject = modal.firstSelection
	end
	-- Gamepad B closes the modal; sink it so it doesn't also trigger gameplay.
	-- (Escape opens Roblox's own menu, so keyboard/mouse players use a visible close button.)
	ContextActionService:BindAction(CLOSE_ACTION, function(_, state)
		if state == Enum.UserInputState.Begin then
			ModalManager.close()
			return Enum.ContextActionResult.Sink
		end
		return Enum.ContextActionResult.Pass
	end, false, Enum.KeyCode.ButtonB)
	if modal.onOpen then
		modal.onOpen()
	end
end

function ModalManager.toggle(name: string)
	if openName == name then
		ModalManager.close()
	else
		ModalManager.open(name)
	end
end

return ModalManager
```
<!-- /code -->

### Usage

```lua
local playerGui = Players.LocalPlayer:WaitForChild("PlayerGui")
local shopGui = playerGui:WaitForChild("Shop") :: ScreenGui
ModalManager.register("Shop", {
	gui = shopGui,
	firstSelection = shopGui:FindFirstChild("BuyButton", true) :: GuiObject?,
	onOpen = function() ShopController.refresh() end,
})
hudShopButton.Activated:Connect(function() ModalManager.toggle("Shop") end)
closeButton.Activated:Connect(ModalManager.close) -- keyboard/mouse/touch close
```

Notes:
- Set `ResetOnSpawn = false` on modal ScreenGuis, or registrations break on respawn.
- Give modal ScreenGuis a higher `DisplayOrder` than the HUD.
- Every modal needs a visible close button. Escape opens Roblox's own menu, so it isn't a reliable close key.

## ButtonFeedback

<!-- code: ReplicatedStorage/Client/UI/ButtonFeedback.luau -->
```lua
--!strict
-- ReplicatedStorage/Client/UI/ButtonFeedback (ModuleScript, client)
-- Hover / press / gamepad-select feedback for any GuiButton via a UIScale child.
-- Respects the player's Reduce Motion setting. Returns a cleanup function.
local GuiService = game:GetService("GuiService")
local TweenService = game:GetService("TweenService")

local HOVER_SCALE = 1.06
local PRESS_SCALE = 0.94
local TWEEN_TIME = 0.08 -- short: feedback must feel instant

local ButtonFeedback = {}

function ButtonFeedback.attach(button: GuiButton): () -> ()
	local scale = button:FindFirstChildOfClass("UIScale") or Instance.new("UIScale")
	scale.Parent = button

	local hovered = false
	local pressed = false
	local currentTween: Tween? = nil

	local function update()
		local target = if pressed then PRESS_SCALE elseif hovered then HOVER_SCALE else 1
		if currentTween then
			currentTween:Cancel()
		end
		if GuiService.ReducedMotionEnabled then
			scale.Scale = target
			return
		end
		local tween = TweenService:Create(
			scale,
			TweenInfo.new(TWEEN_TIME, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
			{ Scale = target }
		)
		currentTween = tween
		tween:Play()
	end

	local connections = {
		button.MouseEnter:Connect(function()
			hovered = true
			update()
		end),
		button.MouseLeave:Connect(function()
			hovered = false
			pressed = false
			update()
		end),
		-- Gamepad / keyboard UI navigation highlight counts as hover.
		button.SelectionGained:Connect(function()
			hovered = true
			update()
		end),
		button.SelectionLost:Connect(function()
			hovered = false
			update()
		end),
		button.MouseButton1Down:Connect(function()
			pressed = true
			update()
		end),
		button.MouseButton1Up:Connect(function()
			pressed = false
			update()
		end),
	}

	return function()
		for _, connection in connections do
			connection:Disconnect()
		end
		if currentTween then
			currentTween:Cancel()
		end
		scale.Scale = 1
	end
end

return ButtonFeedback
```
<!-- /code -->

### Usage

```lua
-- Tag buttons "Pressable" in Studio and attach feedback to all of them.
for _, button in CollectionService:GetTagged("Pressable") do
	if button:IsA("GuiButton") then ButtonFeedback.attach(button) end
end
```

## Polish additions

- **Sound.** Play a short click on `Activated`, not on `MouseButton1Down`, so it plays once per real activation and also works for gamepad and touch. Keep UI sounds quiet and consistent; see [[Audio Design and Implementation]].
- **Disabled state.** Grey the button out, drop the hover scale, and set `Active = false`. Explain *why* it's disabled ("Need 50 more coins").
- **Opening animation.** Fade with `CanvasGroup.GroupTransparency` from 1 to 0 plus `UIScale` 0.9 to 1 over about 0.15–0.25 s, `Quad`/`Back` `Out`. Skip it when `ReducedMotionEnabled` is on. These timings are common practice, not official numbers; keep menus fast.

## Related notes

- [[Roblox UI Engineering Guide]]
- [[UI Visual Design and Polish]]
- [[Controller UI Checklist]]

## Sources

- [GuiService.SelectedObject](https://create.roblox.com/docs/reference/engine/classes/GuiService#SelectedObject), [ContextActionService](https://create.roblox.com/docs/reference/engine/classes/ContextActionService), [Accessibility: Reduce Motion](https://create.roblox.com/docs/production/publishing/accessibility). AUTHORITATIVE.

## Last Verified

2026-10-14
