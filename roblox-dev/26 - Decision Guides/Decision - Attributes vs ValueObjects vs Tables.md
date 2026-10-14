---
title: Decision - Attributes vs ValueObjects vs Tables
category: Decision Guides
tags: [decision, architecture, data]
status: active
source_quality: AUTHORITATIVE facts + labelled judgement
last_verified: 2026-10-14
aliases: [Attributes vs ValueObjects, Where to store state]
---

# Decision - Attributes vs ValueObjects vs Tables

| Store it as | When |
|---|---|
| **Luau table on the server** | Authoritative game state: player data, inventories, cooldowns. Not replicated by default; you choose what to expose. |
| **Attribute** | Small per-instance values that should replicate and be observable (`GetAttributeChangedSignal`): config on tagged objects, an NPC's state for client VFX, a player's replicated currency for the HUD. Lightweight; no extra instances. |
| **ValueObject** (IntValue and similar) | Only where an Instance is required, e.g. the legacy `leaderstats` folder for the built-in leaderboard. Otherwise attributes are lighter. |
| **Remote message** | Private per-player data, or larger or complex structures |

**Server authority model caveat:** synced attributes on predicted instances are limited to the first 64 per instance, with names and string values up to 50 characters ([[Network Ownership and Physics Security]]).

**Tags vs folders:** use **CollectionService tags** to mark *behaviour* ("Door", "Pressable"), and folders and models to organize *structure*. Code queries tags, not paths, so designers can move objects freely and streaming works.

## Sources

[Attributes](https://create.roblox.com/docs/scripting/attributes), [Server authority model](https://create.roblox.com/docs/projects/server-authority), [Leaderboards](https://create.roblox.com/docs/players/leaderboards). AUTHORITATIVE.

## Last Verified

2026-10-14
