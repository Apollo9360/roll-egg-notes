---
title: Recipe - Notification Queue
category: Recipes
tags: [recipe, ui, notifications, toasts, accessibility, luau]
status: active
source_quality: Vault UI principles (accessibility settings are AUTHORITATIVE); code type-checked
last_verified: 2026-10-14
aliases: [Toasts, Toast queue, Notifications, Popup queue]
---

# Recipe - Notification Queue

## Summary

Toasts ("+$50", "Egg placed!", "Not enough Speed") should show **one at a time, briefly, without flooding**. This client module queues them, merges repeats of the same text into "x3", caps the queue so a burst doesn't keep toasts on screen for minutes, and respects the player's **Reduce Motion** setting ([[Accessibility and Localization]]).

The label is an **authored** TextLabel in StarterGui; the module only drives it ([[Roblox UI Engineering Guide]]).

## Code

Type-checked with luau-lsp; not yet run in a live game.

<!-- code: ReplicatedStorage/Client/UI/NotificationQueue.luau -->
```lua
--!strict
-- ReplicatedStorage/Client/UI/NotificationQueue (ModuleScript, client)
-- Shows toasts one at a time from a queue. Repeats of a message that is already
-- waiting are merged ("x3") instead of flooding the screen, and the queue has a
-- cap so a burst can't keep toasts on screen for minutes.
local TweenService = game:GetService("TweenService")
local GuiService = game:GetService("GuiService")

export type Notification = { text: string, count: number, duration: number }

export type NotificationQueue = {
	push: (self: NotificationQueue, text: string, duration: number?) -> (),
	destroy: (self: NotificationQueue) -> (),
}

local DEFAULT_DURATION = 2.5
local MAX_QUEUED = 5
local FADE_SECONDS = 0.2

local NotificationQueue = {}

-- template: an authored TextLabel (in StarterGui) that is hidden by default.
function NotificationQueue.new(template: TextLabel): NotificationQueue
	local queue: { Notification } = {}
	local running = false
	local destroyed = false

	local function fade(label: TextLabel, transparency: number)
		-- Honour the player's Reduce Motion setting: switch instantly.
		local seconds = if GuiService.ReducedMotionEnabled then 0 else FADE_SECONDS
		local tween = TweenService:Create(label, TweenInfo.new(seconds), {
			TextTransparency = transparency,
			TextStrokeTransparency = transparency,
		})
		tween:Play()
		if seconds > 0 then
			tween.Completed:Wait()
		end
	end

	local function run()
		running = true
		while #queue > 0 and not destroyed do
			local item = table.remove(queue, 1) :: Notification
			template.Text = if item.count > 1 then `{item.text} x{item.count}` else item.text
			template.TextTransparency = 1
			template.TextStrokeTransparency = 1
			template.Visible = true
			fade(template, 0)
			task.wait(item.duration)
			fade(template, 1)
			template.Visible = false
		end
		running = false
	end

	local self = {} :: NotificationQueue

	function self.push(_self: NotificationQueue, text: string, duration: number?)
		if destroyed then
			return
		end
		for _, item in queue do
			if item.text == text then
				item.count += 1
				return
			end
		end
		if #queue >= MAX_QUEUED then
			table.remove(queue, 1) -- drop the oldest waiting toast
		end
		table.insert(queue, { text = text, count = 1, duration = duration or DEFAULT_DURATION })
		if not running then
			task.spawn(run)
		end
	end

	function self.destroy(_self: NotificationQueue)
		destroyed = true
		table.clear(queue)
		template.Visible = false
	end

	return self
end

return NotificationQueue
```
<!-- /code -->

Usage:

```lua
local toasts = NotificationQueue.new(playerGui.Hud.Toast)
toasts:push("Egg placed!")
toasts:push("Not enough Speed", 1.5)
```

## Notes

- Keep toast text short. Long or important messages belong in a modal ([[Recipe - UI Modal Manager and Button Feedback]]).
- Put the label where it can't cover touch controls, and inside the safe area (`ScreenInsets`).
- Don't use colour alone to tell success from failure; add an icon or a word.
- Server-triggered toasts arrive over a remote; validate nothing on the client, just display. Never let a toast be the only record of a reward.
- For several toasts on screen at once, extend the module with a pool of labels in a `UIListLayout` ([[Recipe - Object Pool]]).

## Common mistakes

- Every event spawning its own toast, so twenty overlap on a big payout.
- Toasts with `TextScaled` that ignore the player's text size setting.
- Tweens that ignore Reduce Motion.

## Related notes

- [[Roblox UI Engineering Guide]]
- [[UI Visual Design and Polish]]
- [[UI Checklist]]

## Sources

- Built on [[Accessibility and Localization]] and [[Roblox UI Engineering Guide]], which cite the official docs.

## Last Verified

2026-10-14
