---
title: Testing and QA Guide
category: Testing & QA
tags: [testing, qa, unit-tests, multiplayer, network-simulator, device-simulator, regression]
status: active
source_quality: AUTHORITATIVE (Studio tools) + USEFUL (test frameworks)
last_verified: 2026-10-14
aliases: [QA, Unit tests, TestEZ, Jest Lua, Multiplayer testing, Device testing]
---

# Testing and QA Guide

## Summary

Test in layers:
1. **Pure-logic unit tests** for formulas, loot tables and validation.
2. **Studio multiplayer playtests** with Server & Clients.
3. **Simulated bad conditions**: Network Simulator, Device Simulator, Controller Emulator.
4. **Real low-end devices.**
5. **Persistence and purchase scenarios**, in a **separate test universe** that can never touch live data.

Automate what repeats, with StudioTestService and VirtualInput.

## Studio test modes

All from the official docs:

| Need | Tool |
|---|---|
| Solo play | Play / Play Here / Run |
| Multiplayer (server plus up to **8** clients) | **Server & Clients** mode. Usually 1–2 clients is enough; use more for load and replication checks. |
| Switch views mid-test | Client/Server toggle |
| Party APIs | Party Simulator |
| Screen sizes, DPI, touch, on-screen keyboard | **Device Simulator** (not accurate for memory) |
| Gamepad, VR, handheld, TV remote | **Controller Emulator** |
| Latency, jitter, packet loss | **Network Simulator** (presets for wired, Wi-Fi and mobile). Essential for server authority and UnreliableRemoteEvents. |
| Scripted multi-client tests | `StudioTestService`: start a server with up to 8 clients, add players mid-session, pass arguments, trigger disconnects |
| Scripted devices | `StudioDeviceSimulatorService`: cycle presets, orientation, DPI |
| Scripted input | `VirtualInput` from `UserInputService:CreateVirtualInput()`, processed like real hardware |
| Collaborative tests | Team Test |

`StudioTestService`, `StudioDeviceSimulatorService` and `VirtualInput` are **Studio-only** and do nothing in published games.

## Unit testing

- **Design for it.** Put rules in pure ModuleScripts (no Instances, no services): damage formulas, loot rolls with an injected RNG, `Validate`, economy math, state machines. Those can be tested anywhere.
- **Frameworks:**
  - **TestEZ** (Roblox): BDD-style `describe`/`it`/`expect`. Last commit 2023-01, so stable but quiet.
  - **Jest Lua** (jsdotlua): a Jest port. It currently runs only inside Roblox. Last commit 2024-12.

  Neither is a Roblox-official live product. Choose one and pin the version.
- **Run them:**
  - in Studio, via a test-runner Script in ServerScriptService that only runs in Studio (`RunService:IsStudio()`); or
  - in CI with Rojo, through a cloud or headless runner (see [[Tooling and Workflow]]).
- **Seed randomness.** Pass a `Random.new(seed)` object into the code so loot and drop tests are deterministic.

## Manual QA passes

Run these per feature, then per release.

1. **Happy path** on PC, phone (Device Simulator, then a real phone) and gamepad.
2. **Multiplayer:** 2+ clients see the same state; late joiners see the correct state.
3. **Lifecycle:** respawn, reset character, leave mid-action, rejoin quickly, teleport.
4. **Persistence:**
   - first join;
   - returning player;
   - an old-schema save (does migration work?);
   - DataStore failure: turn off Studio API access, and the profile should be errored with no overwrite;
   - kill the server mid-save.
5. **Purchases** (test universe): buy, leave before grant, rejoin and confirm exactly one grant; fake products with a missing handler.
6. **Network:** Network Simulator on a bad mobile preset. Is the UI still honest (pending states, retries)?
7. **Exploit pass:** in a Studio client, fire every remote with:
   - wrong types, NaN, inf, huge strings, negative and fractional numbers;
   - tables posing as Instances;
   - spam at about 100/s;
   - prompts and ClickDetectors from far away.

   Everything must be rejected with no errors and no state change ([[Remote Security Checklist]]).
8. **Performance:** MicroProfiler on the baseline device; leave it running for 30 minutes and watch LuaHeap and InstanceCount ([[Performance Checklist]]).
9. **Accessibility and readability:** smallest phone, UI scale, colour-blind-safe signals, text not clipped in longer languages.

## Regression and staging

- **Separate test universe** (a copy of the game) for anything touching data or purchases. Use the same code and different DataStores.
- **Version history:** Studio's place version history lets you roll back a bad publish.
- **A regression list** (`QA Regression List.md` per game) of bugs that shipped once, each with exact repro steps. Re-run it before every release.

## Related notes

- [[Debugging Guide]]
- [[Release Checklist]]
- [[Remote Security Checklist]]
- [[Roblox Stability Engineering]]

## Sources

- [Studio testing modes](https://create.roblox.com/docs/studio/testing-modes), [Network Simulator](https://create.roblox.com/docs/studio/network-simulator), [Device Simulator](https://create.roblox.com/docs/studio/device-simulator), [Controller Emulator](https://create.roblox.com/docs/studio/controller-emulator). AUTHORITATIVE, 2026-10-13.
- [TestEZ](https://github.com/Roblox/testez) (last commit 2023-01-30) and [Jest Lua](https://github.com/jsdotlua/jest-lua) (last commit 2024-12-23). USEFUL.

## Last Verified

2026-10-14
