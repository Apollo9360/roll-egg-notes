---
title: UI Architecture Patterns
category: UI UX
tags: [ui, architecture, state, fusion, react-lua, controllers, components]
status: active
source_quality: HIGH CONFIDENCE (library repos) + AUTHORITATIVE (Roblox docs)
last_verified: 2026-10-14
aliases: [State-driven UI, Fusion, React Lua, Roact, UI controllers, Procedural vs authored UI]
---

# UI Architecture Patterns

## Summary

There are three viable approaches:
1. **Authored instances plus controllers.** UI is built in Studio, and code only binds behaviour. Simple and visual.
2. **Declarative reactive UI** with **Fusion** or **React-Lua**. UI is a function of state. Suited to large, data-heavy UIs.
3. **A hybrid:** authored templates cloned and filled by controllers.

Whichever you choose, keep **one source of state**, update UI **on change** (not per frame), and keep UI code out of game logic.

## Option comparison

| Approach | Strengths | Weaknesses | Pick when |
|---|---|---|---|
| Authored in StarterGui + controllers | WYSIWYG; designers can edit; works with the Style Editor and tokens; no dependencies | Manual sync between state and UI; lists need templates | Most small and medium games; art-driven UIs |
| Templates + controllers (hybrid) | Authored look; data-driven lists | Clone and pool management | Inventories, shops, leaderboards |
| **Fusion** (Elttob; MIT; repo active 2026-02, `0.4.0-dev`) | Reactive state, computeds and springs; Luau-first; small | Pre-1.0, so APIs can change between versions; less visual authoring | Code-first teams wanting reactive state and animation |
| **React-Lua** (Roblox's maintained React port; replaces deprecated **Roact**) | Familiar React model, large-app patterns, used by Roblox | Heavier; more boilerplate; React mental model required | Big apps, or teams with React experience |
| Roact | **Deprecated**: "no longer maintained, see react-lua" | Don't use for new work | Never for new work |

## Principles

1. **One source of truth per value.** For example, the server replicates coins as an attribute, and the HUD listens to `GetAttributeChangedSignal("Coins")`. The UI never stores its own copy that can drift.
2. **Event-driven updates.** Attribute or property changed signals, remote messages, or library reactivity. No `RenderStepped` polling for values that change rarely.
3. **Controllers own screens.** `ShopController` finds the Shop ScreenGui, binds buttons, renders data and sends intent. It holds no authoritative state.
4. **Components are reusable**: button, card, tab bar, modal. They are tagged or styled by StyleSheet rules, and created via templates or Fusion/React components.
5. **Cleanup per screen.** Opening a screen creates connections; closing it cleans them ([[Cleanup Pattern (Maid Janitor Trove)]]).
6. **Server boundary.** UI requests go through validated remotes. The UI shows pending, then the server result. It is never the authority.
7. **Pool list items** for long or frequently refreshed lists. Reuse rows instead of destroy and recreate.

## Authored UI binding pattern

```lua
-- ShopController (client): binds authored UI, renders from data, sends intent.
local shopGui = playerGui:WaitForChild("Shop") :: ScreenGui
local list = shopGui:WaitForChild("List") :: ScrollingFrame
local template = list:WaitForChild("ItemTemplate") :: Frame
template.Visible = false

local function render(items: { { id: string, name: string, price: number } })
	for _, row in list:GetChildren() do
		if row:IsA("Frame") and row ~= template then row:Destroy() end -- or pool them
	end
	for _, item in items do
		local row = template:Clone()
		row.Name = item.id
		;(row:WaitForChild("Title") :: TextLabel).Text = item.name
		;(row:WaitForChild("Price") :: TextLabel).Text = tostring(item.price)
		row.Visible = true
		row.Parent = list
	end
end
```

## Related notes

- [[Roblox UI Engineering Guide]]
- [[UI Visual Design and Polish]]
- [[Roblox Project Architecture]]

## Sources

- [Fusion repo](https://github.com/dphfox/Fusion) (wally `0.4.0-dev1`, last commit 2026-02-01), [React-Lua](https://github.com/jsdotlua/react-lua), [Roact README: deprecated](https://github.com/Roblox/roact). HIGH CONFIDENCE.
- [Roblox UI docs](https://create.roblox.com/docs/ui). AUTHORITATIVE.

## Last Verified

2026-10-14
