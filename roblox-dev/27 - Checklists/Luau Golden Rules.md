---
title: Luau Golden Rules
category: Checklists
tags: [quick-reference, luau]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Luau rules]
---

# Luau Golden Rules

- **`--!strict` everywhere.** Annotate module APIs; narrow `Instance?` before use.
- **Use the `task` library:** `task.wait`, `task.spawn`, `task.defer`, `task.delay`, `task.cancel`. Never `wait`, `spawn` or `delay`.
- **`typeof()` for Roblox types**, not `type()`.
- **Iterate with `for k, v in t do`.** Use `ipairs` only when you need stop-at-nil.
- **Build tables in one literal; `table.create(n)` for known sizes; `table.insert` to append.**
- **Methods on a metatable with `__index = Class`**, one level deep. Call `obj:Method()`.
- **No `getfenv`, `setfenv` or `loadstring`.** They deoptimize the whole script.
- **No allocation in hot loops.** No temporary tables, closures or string building per frame.
- **`pcall` only around external calls**: DataStore, HTTP, Marketplace, Teleport. `assert` invariants.
- **Write for `SignalBehavior = Deferred`.** Use `:Once()` for first-fire handlers.
- **Time sources:**
  - `os.clock()` for local durations;
  - `workspace:GetServerTimeNow()` for times shared between client and server;
  - `time()` inside server-authority simulation.
- **`@native` only on proven hot numeric server functions.**
- **Checker quirk:** `local ok, err: any = pcall(f)` when `f` returns nothing.

Details: [[Luau Language and Performance Guide]] · [[Production Luau Coding Standards]]

## Sources

[Luau performance](https://luau.org/performance), [Roblox task library](https://create.roblox.com/docs/scripting/scheduler), [Deferred events](https://create.roblox.com/docs/scripting/events/deferred). AUTHORITATIVE.

## Last Verified

2026-10-14
