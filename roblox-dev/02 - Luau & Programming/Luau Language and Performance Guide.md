---
title: Luau Language and Performance Guide
category: Luau & Programming
tags: [luau, types, strict, performance, native, task-library, events]
status: active
source_quality: AUTHORITATIVE (Luau team docs, Roblox docs)
last_verified: 2026-10-14
aliases: [Typed Luau, --!strict, Luau performance, task library, native code generation]
---

# Luau Language and Performance Guide

## Summary

Write `--!strict` typed Luau. Use the `task` library (never `wait`, `spawn` or `delay`), generalized iteration, `table.create` for known sizes, `obj:Method()` calls, metatables whose `__index` points straight at a table, and **no allocation in hot loops**.

Measure before optimizing: [[Performance and Profiling Guide]]. Use `--!native` or `@native` only on proven numeric hot spots in **server** scripts.

## Type checking

- **Modes:** `--!nocheck`, `--!nonstrict` (the default) and `--!strict`. Use **strict** for new code; it catches nil-access, wrong-type and arity bugs before runtime.
- **Annotate module boundaries:** function parameters and returns, exported types (`export type Profile = {...}`), and remote payload types.
- **Narrow `Instance?` results:**
  ```lua
  local root = character:FindFirstChild("HumanoidRootPart")
  if not root or not root:IsA("BasePart") then return end
  -- root is BasePart here
  ```
- **`:: Type` casts are assertions, not conversions.** Use them sparingly and only where you know more than the checker. The usual cases are `WaitForChild` results and library types that don't unify.
- **Types don't exist at runtime.** Anything crossing the network must still be validated with `typeof` ([[Recipe - Secure Remote Handler]]).
- **Generics:** `function first<T>(list: {T}): T?`.
- **Unions and intersections, refinement:** `type Shape = Circle | Square` narrowed by `if shape.kind == "circle"`.
- **Checker quirk** (luau-lsp 1.70, 2026-10): `local ok, err = pcall(f)` where `f` returns nothing reports "Function only returns 1 value". Write `local ok, err: any = pcall(f)`.

## Scheduling: the task library

| Legacy (don't use) | Use | Behaviour |
|---|---|---|
| `wait()` / `wait(n)` | `task.wait(n)` | Resumes on the next Heartbeat after n seconds; returns the actual time elapsed |
| `spawn(f)` | `task.defer(f, ...)` | Runs at the end of the current resumption point |
| `delay(n, f)` | `task.delay(n, f, ...)` | Runs after n seconds |
| (none) | `task.spawn(f, ...)` | Runs **immediately**, through the scheduler |
| (none) | `task.cancel(thread)` | Stops a scheduled or yielded thread |

- `task.wait()` with no argument is roughly one Heartbeat.
- Prefer **events** to polling loops; see [[Decision - Polling vs Events]].

## Events: deferred signal behaviour

- `Workspace.SignalBehavior = Deferred` is recommended, and new template places use it. `Default` currently equals Immediate but **will switch to Deferred**, so write code that works under Deferred now.
- With Deferred:
  - handlers run at the next resumption point (input processing, PreRender, PreAnimation, PreSimulation, PostSimulation, Heartbeat, task resumes, BindToClose);
  - "fire, then read a flag the handler sets" patterns break;
  - `Destroying` handlers run **after** the instance is destroyed.
- Use `signal:Once(fn)` for first-occurrence handlers. `Disconnect()` drops queued invocations; `Destroy()` disconnects but still runs pending ones.
- Re-entrancy depth limit: 10. Events that keep firing each other stop at that depth.

## Performance rules from the Luau team

| Do | Why |
|---|---|
| `t.field` (constant keys); uniform object shapes | Inline caching of field lookup |
| Methods on the metatable, `__index = Class` (a table, not a function), shallow chains | Fast method-call path |
| Call `obj:Method()`, not `obj.Method(obj)`; **don't** cache methods in locals | The namecall path is already optimal |
| Call builtins directly, e.g. `math.max(x, 1)`. `string.byte(s)` beats `s:byte()` | "Fastcall" builtins |
| `for k, v in t do` | As fast as `pairs`/`ipairs`, and skips the setup call. Use `ipairs` only when you need stop-at-nil. |
| `table.create(n)` then `t[i] = ...`; table literals with all fields at once | Preallocation and table templates |
| `table.insert(t, v)` when the size is unknown | Fastest append |
| `#t` freely | Usually O(1); cached |
| Native `vector`/`Vector3` math | Value type, no GC pressure |
| **Avoid** `getfenv`, `setfenv`, `loadstring` | They deoptimize the whole environment and disable inlining and native code |
| **Avoid** allocating in hot loops (temporary tables, closures, string concatenation) | GC "assists" steal frame time |
| Weak caches: `__mode = "ks"` / `"vs"` (`s` = shrinkable) | Lets the GC shrink large weak tables |

### Native code generation

**When it helps.** Native code generation compiles a server script or function to machine code. Use it for heavy numeric work on tables and `buffer`s that runs many times (custom physics, pathfinding math, procedural generation). Top-level code gains little; it runs once.

**Costs:**
- longer server startup;
- extra memory;
- a **game-wide size limit** on native code.

So never put `--!native` in every script. Use `@native` on specific functions.

**What de-optimizes it:**
- `getfenv`/`setfenv`;
- math builtins called with non-numbers;
- arguments that don't match the annotated types;
- breakpoints, which disable native execution for that function.

**Tips:**
- Annotate `Vector3` parameters; otherwise the compiler assumes a table.
- The Script Profiler marks native functions with `<native>`.
- `debug.dumpcodesize()` in the Command Bar (Server view) reports native code size against the limit.

## Metatables and OOP

```lua
--!strict
local Projectile = {}
Projectile.__index = Projectile

export type Projectile = typeof(setmetatable({} :: {
	position: Vector3,
	velocity: Vector3,
}, Projectile))

function Projectile.new(position: Vector3, velocity: Vector3): Projectile
	-- all fields in one literal = table template optimization
	return setmetatable({ position = position, velocity = velocity }, Projectile)
end

function Projectile.step(self: Projectile, dt: number)
	self.position += self.velocity * dt
end

return Projectile
```

Prefer composition (small modules with data tables plus functions) over deep inheritance. One level of `__index` is ideal.

## Error handling

- `pcall` or `xpcall` around anything that can fail and isn't your bug: DataStore, HTTP, Marketplace, `require` of optional modules, user callbacks.
- `assert(cond, msg)` for programmer errors and invariants. It's cheap: specialized when the result is unused.
- `error({ code = "NotEnoughCoins" })` lets callers branch on structured errors.
- Never silently swallow errors. At minimum `warn` with context such as the player and IDs.

## Common mistakes

- `wait()` and `spawn()` in new code.
- Polling `while true do task.wait(0.1)` for state that has a `Changed` event or attribute signal.
- Assuming a `Destroying` handler can still read children (under Deferred it can't).
- Recreating closures for `table.sort` comparators in a hot loop.
- Using `type()` for Roblox types. Use `typeof()`; `type(Vector3)` is `"userdata"`.

## Related notes

- [[Production Luau Coding Standards]]
- [[Performance and Profiling Guide]]
- [[Roblox Project Architecture]]
- [[Luau Golden Rules]]

## Sources

- [Luau: How we make Luau fast (performance guide)](https://luau.org/performance). Luau team, site repo 2026-10-09. AUTHORITATIVE.
- [Luau type checking docs](https://luau.org/typecheck). AUTHORITATIVE.
- [Schedule code (task library)](https://create.roblox.com/docs/scripting/scheduler). AUTHORITATIVE.
- [Deferred engine events](https://create.roblox.com/docs/scripting/events/deferred). AUTHORITATIVE.
- [Native code generation](https://create.roblox.com/docs/luau/native-code-gen). AUTHORITATIVE.

## Last Verified

2026-10-14. The OOP example was checked with luau-lsp in strict mode.
