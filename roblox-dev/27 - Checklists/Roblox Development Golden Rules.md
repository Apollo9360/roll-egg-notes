---
title: Roblox Development Golden Rules
category: Checklists
tags: [quick-reference, rules]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Golden rules, Top rules]
---

# Roblox Development Golden Rules

Short enough to load into any AI context. Each rule links to the note that explains it.

1. **The server is the authority.** It owns money, items, damage, cooldowns and progress. Clients send intent ([[Client Server Golden Rules]]).
2. **Validate every client input:** rate, type, value and context. NaN passes `typeof` but fails every comparison, so use `math.isfinite` ([[Remote Security Checklist]]).
3. **One writer per piece of state,** and no yield between check and write ([[Production Luau Coding Standards]]).
4. **Load player data once, session-locked, one key per player.** Save on interval, leave, shutdown and purchase. Never overwrite real data with defaults ([[DataStore Checklist]]).
5. **Grant Robux purchases only in `ProcessReceipt`,** recording the PurchaseId and confirming the save ([[Developer Product Receipt Pipeline]]).
6. **Every connection and resource has an owner and an end.** Clean up per-player state on PlayerRemoving ([[Cleanup Pattern (Maid Janitor Trove)]]).
7. **Event-driven over polling.** Nothing expensive runs every frame unless it must ([[Decision - Polling vs Events]]).
8. **Visuals on clients** (VFX, tweens, sounds, NPC crowd animation); outcomes on the server ([[Performance Checklist]]).
9. **Design for the phone first:** safe areas, scale-based UI, big touch targets, low memory ([[Mobile UI Checklist]]).
10. **Every action works on touch, gamepad and keyboard** ([[Controller UI Checklist]]).
11. **Measure before optimizing,** on a low-end device ([[Performance and Profiling Guide]]).
12. **Use `--!strict` typed Luau, the `task` library, and no deprecated APIs** ([[Luau Golden Rules]]).
13. **Single bootstrap per side,** ModuleScripts with `start()`, and tags instead of scripts in Workspace ([[Roblox Project Architecture]]).
14. **Test in Server & Clients, with Network Simulator, on a real phone, in a test universe** ([[Release Checklist]]).
15. **Show the core fantasy.** The game must read in one image and in the first minute ([[Show the core fantasy and loop]]).
16. **Never invent facts.** Check the official docs; label anything unverified ([[CONTRIBUTING TO THE ROBLOX DEVELOPMENT VAULT]]).

## Sources

Distilled from this vault's AUTHORITATIVE-sourced notes; see each linked note.

## Last Verified

2026-10-14
