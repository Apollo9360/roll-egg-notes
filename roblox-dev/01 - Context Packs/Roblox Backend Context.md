---
title: Roblox Backend Context
category: Context Packs
tags: [context-pack, backend, server, networking, data, architecture]
status: active
source_quality: Distilled from the linked vault notes (sources cited there)
last_verified: 2026-10-14
aliases: [Backend context, Server context]
---

# Roblox Backend Context

## Summary

Load this before writing server code, remotes, services or player data.

## Load these notes, in order

1. [[Roblox Project Architecture]]: one server Script, one client Script, everything else in ModuleScripts with `start()`.
2. [[Roblox Networking Best Practices]] and [[Decision - Which Remote Type]].
3. [[Remote Security and Anti-Exploit]].
4. [[Data Persistence Guide]] and [[Decision - DataStore vs MemoryStore]].
5. [[Developer Product Receipt Pipeline]] if anything is bought with Robux.
6. [[Roblox Stability Engineering]] and [[Production Luau Coding Standards]].

## Non-negotiables

- The server owns game state. Clients send **intent**; the server validates, changes state and replicates the result.
- Validate every remote in this order: rate limit → types → values → context.
- Load player data **once**, under **one key**, with a **session lock** (ProfileStore). Save on an interval, on leave, on shutdown and at purchase checkpoints.
- Migrate old saves **before** reconciling them with the template, so a missing schema version isn't filled in by the default.
- `ProcessReceipt` grants once per `PurchaseId` and returns `PurchaseGranted` only after the grant is saved.
- Everything you connect or create has a cleanup path ([[Cleanup Pattern (Maid Janitor Trove)]]).

## Key numbers

| Fact | Value | Note |
|---|---|---|
| `BindToClose` time budget | 30 s | [[Data Persistence Guide]] |
| `UnreliableRemoteEvent` payload limit | 1,000 bytes | [[Roblox Networking Best Practices]] |
| Frame budget at 60 FPS | 16.67 ms; server heartbeat capped at 60 | [[Performance and Profiling Guide]] |

DataStore, MemoryStore and MessagingService limits change with player count; read them in [[Data Persistence Guide]] instead of copying them here.

## Checked code to start from

- [[Recipe - Secure Remote Handler]]
- [[Recipe - Player Data with ProfileStore]]
- [[Developer Product Receipt Pipeline]] (ReceiptHandler)
- [[Teleportation and Cross-Server]] (SafeTeleport)

## Checklists to run

[[Client Server Golden Rules]] · [[Remote Security Checklist]] · [[DataStore Checklist]] · [[Production Data Persistence Checklist]] · [[Code Review Checklist]]

## Common AI mistakes

- Adding a second data path or a second remote for something an existing service already does.
- Trusting a price, amount or target sent by the client.
- Writing `SetAsync` on leave with no session lock.
- Teleporting before the final save is queued.

## Related notes

- [[START HERE]]
- [[AI Roblox Development Playbook]]
- [[Roblox Development Golden Rules]]

## Sources

The notes linked above. Each cites its official sources.

## Last Verified

2026-10-14. Facts here are copied from the linked notes, which cite their sources. If a number here and a note disagree, the note wins; fix this pack.
