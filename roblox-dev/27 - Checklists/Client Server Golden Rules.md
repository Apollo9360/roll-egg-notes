---
title: Client Server Golden Rules
category: Checklists
tags: [quick-reference, networking, security]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Client server rules, Should this run on the client or server]
---

# Client Server Golden Rules

| Server | Client |
|---|---|
| Currency, inventory, progression, purchases | UI, input, camera |
| Damage, health, cooldowns, hit validation | Animation for own character; VFX; SFX; tweens |
| NPC decisions, pathing, physics ownership for gameplay objects | Prediction and cosmetic interpolation |
| DataStores, MemoryStores, MessagingService, Teleports, Analytics | Local settings UI (persisted through the server) |
| Spawning replicated instances | Client-only effects that don't need to replicate |

- **Remote messages are intent:** `RequestPurchase(itemId, qty)`, never `SetCoins`.
- **Choose the remote by its guarantee:**
  - RemoteEvent (reliable, ordered);
  - UnreliableRemoteEvent (continuous data, 1,000-byte limit, dropped when over);
  - RemoteFunction (C→S request-response only; never `InvokeClient` for important flows).
- **About 500 client→server requests per second, shared per remote type.** Keep traffic far below that.
- **Server relays are gatekeepers.** Validate, then `FireAllClients`.
- **Replicated containers can be decompiled.** No secrets in ReplicatedStorage modules.

Details: [[Roblox Networking Best Practices]] · [[Remote Security and Anti-Exploit]] · [[Decision - Which Remote Type]]

## Sources

[Remote events](https://create.roblox.com/docs/scripting/events/remote), [Security tactics](https://create.roblox.com/docs/scripting/security/security-tactics). AUTHORITATIVE.

## Last Verified

2026-10-14
