---
title: Roblox Project Architecture
category: Architecture
tags: [architecture, modules, services, controllers, collectionservice, bootstrapping, frameworks]
status: active
source_quality: AUTHORITATIVE (Roblox docs) + HIGH CONFIDENCE (library authors)
last_verified: 2026-10-14
aliases: [Project structure, Services and controllers, Single entry point, Knit alternative, Code organization]
---

# Roblox Project Architecture

## Summary

Roblox's own recommendation:
- **one** server `Script` in `ServerScriptService`;
- **one** client `Script` with `RunContext = Client` in `ReplicatedStorage` (or a LocalScript in `StarterPlayerScripts`);
- **everything else in ModuleScripts** that expose `start()`.

Require all modules first, then start them in a defined order. Use **CollectionService tags** instead of scripts inside Workspace objects.

Frameworks are optional. Knit, the most popular one, is **archived**, and its author recommends plain ModuleScripts plus thin typed remote wrappers.

## Where code lives

| Container | Replicates to client? | Put here |
|---|---|---|
| `ServerScriptService` | No; can't be decompiled | Server entry Script, services, server-only modules, secrets logic |
| `ServerStorage` | No | Server-only assets: maps, NPC templates, loot tables |
| `ReplicatedStorage` | Yes, and can be decompiled | Shared modules (types, constants, pure logic, validation), remotes, client entry Script, client modules, shared assets |
| `ReplicatedFirst` | Yes, **first** | The loading screen only: one client script and a minimal set of modules |
| `StarterPlayerScripts` | Copied per player | LocalScripts if you don't use a RunContext-Client Script |
| `StarterCharacterScripts` | Copied per character spawn | Per-character client logic; re-runs on every respawn |
| `StarterGui` | Copied per player (on spawn, unless `ResetOnSpawn` is false) | Authored UI instances |
| `Workspace` | Yes | World geometry. **No scripts inside models**; tag them instead. |

From the official "Script types and locations" docs:
- A `Script` with `RunContext = Legacy` (the default) runs only in server containers.
- `RunContext = Client` lets a Script run from ReplicatedStorage. In `Starter*` containers both the original and the copy would run, so use LocalScripts there.
- Client code in ReplicatedStorage gives stable Output links (`ReplicatedStorage.X` instead of `Players.Name.PlayerScripts.X`).
- Never put server logic in a ModuleScript that replicates. It can be decompiled; see [[Remote Security and Anti-Exploit]].

## Recommended layout

```text
ReplicatedFirst/
  LoadingScreen (Script, RunContext=Client)
ReplicatedStorage/
  Client/
    Main (Script, RunContext=Client)  -- single client entry point
    Controllers/  UIController, CameraController, InputController, ShopController ...
  Shared/         Types, Constants, Config, Validate, Cleanup, pure game rules
  Remotes/        RemoteEvents/Functions (created by the server or authored)
  Assets/         Shared models, VFX, sounds referenced by clients
ServerScriptService/
  Main (Script)   -- single server entry point
  Services/       PlayerData, ReceiptHandler, ShopService, CombatService, NPCService ...
  Packages/       Third-party server modules (ProfileStore ...)
ServerStorage/
  Maps/, NPCTemplates/, LootTables/
StarterGui/       Authored ScreenGuis (Hud, Menus); controllers bind to them
```

## Bootstrapping

1. **Phase 1, require.** Modules do **no work at require time**: no connections, no yields, no DataStore calls. They return a table with `start()`, and optionally `init()` for dependency wiring.
2. **Phase 2, start**, in explicit order. Data comes first; anything that reads player data starts after it. Wrap each `start` in `pcall` so one broken service doesn't take down the rest.

<!-- code: ServerScriptService/Main.server.luau -->
```lua
--!strict
-- ServerScriptService/Main (Script, RunContext = Server): the ONLY server entry point.
-- Phase 1 requires every service (no side effects at require time),
-- phase 2 starts them in dependency order. Data first, purchases right after.
local ServerScriptService = game:GetService("ServerScriptService")
local Services = ServerScriptService.Services

local PlayerData = require(Services.PlayerData)
local ReceiptHandler = require(Services.ReceiptHandler)

local startOrder: { { name: string, start: () -> () } } = {
	{ name = "PlayerData", start = PlayerData.start },
	{ name = "ReceiptHandler", start = ReceiptHandler.start },
}

for _, service in startOrder do
	-- One broken service must not stop the others from starting.
	local ok, err: any = pcall(service.start)
	if not ok then
		warn(`[Main] {service.name}.start failed: {err}`)
	end
end
```
<!-- /code -->

The client does the same:
1. Wait for critical data, such as the replicated profile or attributes.
2. Start controllers.
3. Hide the loading screen.

## Services, controllers and dependencies

- **Service** (server): owns one domain's authoritative state and rules: shop, combat, data, NPCs. It exposes a small API to other services and listens to its own remotes.
- **Controller** (client): owns one domain's presentation and input: a UI screen, the camera, a VFX player. It sends **intent** to services and renders replicated state.
- **Shared module**: types, constants and pure functions (damage formulas, loot tables, validation). It is safe on both sides because it holds no state and no secrets.
- **Dependency direction:** controllers → shared; services → shared and other services; shared → nothing game-specific. **Never** server → client modules or shared → service.
- **Circular requires** error at runtime. Fix them by moving the shared piece into a third module, or by passing a dependency in `init(deps)`.

## Tags instead of per-instance scripts

```lua
-- One module manages every tagged instance (official pattern)
local CollectionService = game:GetService("CollectionService")
for _, door in CollectionService:GetTagged("Door") do setUpDoor(door) end
CollectionService:GetInstanceAddedSignal("Door"):Connect(setUpDoor)
CollectionService:GetInstanceRemovedSignal("Door"):Connect(cleanUpDoor)
```

- Tag in Studio (Properties → Tags) or with `Instance:AddTag`.
- Keep per-instance config in **attributes** (`door:GetAttribute("OpenTime")`).
- This works with StreamingEnabled: instances stream in and out and fire added/removed signals, so always handle removal. Pair it with [[Cleanup Pattern (Maid Janitor Trove)]].

## State: attributes vs ValueObjects vs tables

See [[Decision - Attributes vs ValueObjects vs Tables]]. In short:
- **Attributes** for small replicated per-instance state and config.
- **Luau tables** on the server for authoritative data.
- **Remotes** for private or complex per-player data.
- ValueObjects only where an `Instance` is required, e.g. legacy `leaderstats`.

## Frameworks and libraries

| Option | Status (2026-10) | Notes |
|---|---|---|
| Plain ModuleScripts with start/init | Recommended by Roblox and by Knit's author | No dependency; full Luau typing |
| **Knit** (sleitnick) | **Archived 2024-07** | Author: types and intellisense can't flow through Knit's design; ModuleScripts already cover "services". Don't start new projects on it. |
| Trove, Signal, Comm, TypedRemote and others ([RbxUtil](https://github.com/Sleitnick/RbxUtil)) | Maintained (repo updated 2026-07) | Small focused utilities: cleanup, signals, typed remotes |
| Janitor (howmanysmall) | Maintained (2025-10) | Cleanup |
| ProfileStore | Maintained | Player data; see [[Recipe - Player Data with ProfileStore]] |
| Fusion, React-Lua | See [[UI Architecture Patterns]] | Declarative UI |

Networking libraries are, in Knit's author's words, "a wrapper on top of RemoteEvents... Luau's structural typings do not enforce the underlying data". **Any typed remote layer still needs runtime validation** on the server ([[Recipe - Secure Remote Handler]]).

## Avoid giant manager scripts

Signs of trouble:
- one script over about 1,000 lines handling several domains;
- `_G` or `shared` used as a service locator;
- scripts inside many Workspace models;
- remotes created ad hoc in various places.

Split by domain into services and controllers, with one bootstrap and tags for world objects.

## Rojo or Studio-only?

See [[Tooling and Workflow]]. Either works with this architecture. Rojo with Git adds code review, diffs, CI (linting, type-checking, tests) and Wally packages. Studio-only keeps UI and world authoring simple. Many teams use Rojo for code and Studio for assets.

## Common mistakes

- Doing work at require time, which causes order bugs and yields inside `require`.
- `WaitForChild` chains everywhere instead of one bootstrap that creates what it needs.
- Shared modules that secretly hold mutable state used by both sides. On each side it is a separate copy.
- Copy-pasted logic in every tagged model's script.
- Using `_G` for cross-module communication.

## Related notes

- [[Production Luau Coding Standards]]
- [[Luau Language and Performance Guide]]
- [[Cleanup Pattern (Maid Janitor Trove)]]
- [[Roblox Networking Best Practices]]
- [[Tooling and Workflow]]
- [[Roblox Stability Engineering]]

## Sources

- [Script types and locations](https://create.roblox.com/docs/scripting/locations). AUTHORITATIVE.
- [Data model](https://create.roblox.com/docs/projects/data-model). AUTHORITATIVE.
- [Access control and confidentiality (decompilation)](https://create.roblox.com/docs/scripting/security/access-control). AUTHORITATIVE.
- [Knit ARCHIVAL.md](https://github.com/Sleitnick/Knit/blob/main/ARCHIVAL.md) by sleitnick (2024-07-31). HIGH CONFIDENCE; primary source on Knit's status.
- [RbxUtil](https://github.com/Sleitnick/RbxUtil), [Janitor](https://github.com/howmanysmall/Janitor). USEFUL.

## Last Verified

2026-10-14
