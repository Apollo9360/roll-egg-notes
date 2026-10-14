---
title: Network Ownership and Physics Security
category: Security
tags: [security, physics, network-ownership, server-authority, movement]
status: active
source_quality: AUTHORITATIVE
last_verified: 2026-10-14
aliases: [Network ownership, SetNetworkOwner, Server Authority model, Movement validation]
---

# Network Ownership and Physics Security

## Summary

Roblox splits physics between the server and clients. A client that **owns** an unanchored assembly simulates it with no latency, and it can also send any data it likes for that assembly. Anchored parts are always server-owned.

Control ownership deliberately for gameplay-critical physics:
- `BasePart:SetNetworkOwner(player)` gives ownership to a player.
- `BasePart:SetNetworkOwner(nil)` keeps it on the server.
- `SetNetworkOwnershipAuto()` returns it to the engine's automatic choice.

Never trust physics results from client-owned parts.

## How ownership works

These are official facts:
- By default the engine assigns ownership of unanchored parts to clients whose characters are nearby, depending on client hardware and proximity.
- The server always owns anchored parts; you can't change that.
- Setting ownership on one assembly in a mechanism with no anchored parts sets it for **every assembly** in the mechanism.
- Anchoring a lone assembly makes it server-owned. Unanchoring it **loses** the previous manual setting and returns to automatic.
- `Touched` is tied to ownership: a client can fire `Touched` for parts it owns, even if the server never saw contact.

### Debugging

In a playtest, open the viewport **visualization options** menu and turn on **Network owners**. With multiple clients, each client gets its own colour, mirrored in the Server view.

## When to set ownership manually

| Situation | Do |
|---|---|
| Vehicle with driver and passenger seats | Give the **driver** ownership when they sit. Otherwise the first passenger owns it and driving feels laggy. Also give the driver ownership of loose parts on the vehicle. Revert with `SetNetworkOwnershipAuto()` when they leave. |
| Gameplay-critical moving objects: balls, carried items, NPCs | `SetNetworkOwner(nil)` keeps them server-owned, at a cost in responsiveness. |
| Cosmetic debris | Leave automatic, or make it client-only. |
| Prompts or click detectors on unanchored parts | Anchor the part, or set the owner to the server. A client could otherwise pull the part close to bypass distance checks. |

## Movement validation without Server Authority

There's no universal solution, so tune it per game:
- **Distance over time** with latency tolerance. Average over time rather than per frame.
- **Leaky-bucket accumulators** absorb legitimate bursts and catch sustained violations.
- **Project onto XZ** to catch horizontal teleports without penalizing jumps and falls.
- **Explicitly exempt** legitimate teleports: set a server flag before your own teleport.
- **Prefer rubber-banding** to the last valid position over kicking.

## Server Authority model

Beta as of 2026-10.

The server simulates everything; clients predict their inputs a few frames ahead and **roll back and resimulate** on misprediction. This removes speed and fly hacks by design.

**Setup.** Set `Workspace.AuthorityMode = Server`. This also enables:
- `NextGenerationReplication`
- `PlayerScriptsUseInputActionSystem`
- `SignalBehavior = Deferred`
- `UseFixedSimulation`
- `StreamingEnabled`

**Writing code for it:**
- Put core logic in functions bound with `RunService:BindToSimulation()`, in a ModuleScript initialized on both client and server.
- Inside the simulation, use `time()`; it is synchronized and rewinds during resimulation. Don't use `tick()`, `os.time()` or `os.clock()`.
- Feed **all** simulation-affecting input through the Input Action System (`InputAction` / `InputContext`, parented under the Player), not `UserInputService.InputBegan`.
- Sync custom state with attributes on predicted instances, written only inside bound simulation functions.
  - Only the first 64 attributes on an instance replicate.
  - Attribute names are limited to 50 characters; string values to 50 characters.
- Force prediction on or off with `RunService:SetPredictionMode()`.
- `task.spawn` inside the simulation still runs under simulation restrictions. Use `task.defer` to leave them.

Use it for competitive FPS, racing, sports and combat. Re-check its status before adopting; it is beta and evolving.

## Common mistakes

- Damage from `Touched` on a client-owned weapon.
- Anchoring and unanchoring a part, then wondering why manual ownership was lost.
- Server-side distance checks against parts the client owns (the client moves them).
- Strict per-frame speed checks that flag laggy players.

## Related notes

- [[Remote Security and Anti-Exploit]]
- [[Combat Systems Engineering]]
- [[NPC and AI Engineering]]
- [[Input Systems]]

## Sources

All AUTHORITATIVE, from creator-docs, 2026-10-13:
- [Network ownership](https://create.roblox.com/docs/physics/network-ownership)
- [Network ownership, movement validation and physics (security)](https://create.roblox.com/docs/scripting/security/network-ownership)
- [Server authority model](https://create.roblox.com/docs/projects/server-authority)

## Last Verified

2026-10-14
