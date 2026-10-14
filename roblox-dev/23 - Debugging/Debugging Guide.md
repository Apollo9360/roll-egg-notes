---
title: Debugging Guide
category: Debugging
tags: [debugging, breakpoints, developer-console, logs, replication, datastore, memory]
status: active
source_quality: AUTHORITATIVE
last_verified: 2026-10-14
aliases: [How to debug, Breakpoints, Developer Console, Log files, Repro steps]
---

# Debugging Guide

## Summary

1. **Reproduce deterministically.**
2. **Find which side is wrong** (server or client) with the client/server toggle.
3. **Narrow it down** with breakpoints, watches and `debug.profilebegin`.
4. **Confirm the fix** in the same repro.

For live issues, use the Developer Console (F9) on the real client, and the server logs.

## Tools

| Tool | Use |
|---|---|
| **Breakpoints** (click the Script Editor gutter; conditional and logpoint variants) | Pause in Play/Run; **Call Stack**, **Watch** and **Breakpoints** windows. Luau breakpoints have no runtime cost when not hit. They disable native code for that function. |
| **Output window** | Errors with stack traces. Enable "Show Stack Lines". Client code in ReplicatedStorage gives stable click-through paths. |
| **Command Bar** | Run Luau in edit mode or against the server or client during play: inspect state, fire test events. |
| **Developer Console** (F9, or type `/console` in chat) in client and Studio | Log (client and server), Memory, Network, Server Jobs, Server Stats, Script Profiler, MicroProfiler |
| **Client/Server toggle** (Studio play) | See the world as the server sees it vs a client: replication bugs, ownership |
| **Visualization options** (viewport) | Network owners, collision fidelity, and more |
| **Log files** | Windows: `%LOCALAPPDATA%\Roblox\logs`; Mac: `~/Library/Logs/Roblox`. iOS and Android steps are in the docs. |
| **MicroProfiler and Script Profiler** | Performance bugs; see [[Performance and Profiling Guide]] |

## Workflow

1. **Write the repro steps first**: exact clicks, which client, timing. If it's intermittent, add logging around the suspected state and run in a loop.
2. **Classify it:**
   - Does it happen in solo Play? Then it's logic.
   - Only in Server & Clients? Replication or ownership.
   - Only live? Data, scale, streaming or devices.
   - Only on mobile? Memory, input or UI scale.
3. **Check the boundary.** Did the client send what you think? Did the server receive it? Print both sides with a shared request ID.
4. **Check lifetimes.** Is the object you're holding still parented? Has the character respawned? Was the part streamed out?
5. **Fix the cause, not the symptom.** A `task.wait(1)` "fix" means a race you haven't understood.
6. **Add the repro to the regression list** ([[Testing and QA Guide]]).

## Specific bug classes

- **Replication:**
  - Created on the client? Then the server never sees it.
  - Changed on the client? Not replicated, except for physics on client-owned parts.
  - Check `Archivable` and the container (ServerStorage never replicates).
- **Remotes:**
  - Is a handler connected? (Messages queue until one is, up to a limit.)
  - Mixed tables or nil holes in arguments?
  - Instances that aren't replicated arrive as nil.
  - See [[Roblox Networking Best Practices]].
- **DataStores:**
  - Studio API access on?
  - Error codes (101–106 are key or value problems; 301–306 are full throttle queues).
  - Request budget: `DataStoreService:GetRequestBudgetForRequestType`.
  - Use Data Stores Manager to inspect the actual stored value and its versions.
- **Memory leaks:**
  - Watch LuaHeap and InstanceCount grow over join/leave or respawn cycles.
  - Take a Luau heap snapshot, repeat the cycle, snapshot again and compare.
  - Look for growing tables keyed by Player or Instance, and connections on Players and characters.
- **Deferred events:** a handler sees a destroyed instance, or reads a flag too early. Re-read [[Luau Language and Performance Guide]].
- **Streaming:** nil parts on the client. Use the Shift+F3 Streaming panel and CollectionService signals.
- **Network ownership:** turn on Network owners visualization. Is a client simulating something it shouldn't?

## Structured logging

```lua
-- One-line, searchable, with IDs. Keep a DEBUG switch for verbose logs.
local DEBUG = false
local function log(system: string, message: string, ...: any)
	print(`[{system}] {message}`, ...)
end
local function debugLog(system: string, message: string, ...: any)
	if DEBUG then log(system, message, ...) end
end
log("Shop", `purchase ok user={player.UserId} item={itemId} qty={qty}`)
```

## Related notes

- [[Bug Investigation Checklist]]
- [[Testing and QA Guide]]
- [[Roblox Stability Engineering]]
- [[Performance and Profiling Guide]]

## Sources

- [Debugging](https://create.roblox.com/docs/studio/debugging), [Developer Console](https://create.roblox.com/docs/studio/developer-console), [Testing modes](https://create.roblox.com/docs/studio/testing-modes), [Data store error codes](https://create.roblox.com/docs/cloud-services/data-stores/error-codes-and-limits). AUTHORITATIVE.

## Last Verified

2026-10-14
