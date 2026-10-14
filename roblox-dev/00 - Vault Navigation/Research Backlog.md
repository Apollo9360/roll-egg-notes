---
title: Research Backlog
category: Vault Navigation
tags: [backlog, open-questions, verification]
status: active
source_quality: Vault record
last_verified: 2026-10-14
aliases: [Open questions, To verify]
---

# Research Backlog

Open questions, contradictions and claims still to verify. Take one, research it against official sources, update the note, and remove it here ([[CONTRIBUTING TO THE ROBLOX DEVELOPMENT VAULT]]).

## Contradictions and errata in official docs

- **Maximum texture resolution:** the docs say both 4096 and 1024 in different places. Test an import and record the result in [[Blender to Roblox Modeling Pipeline]].
- **Game design doc erratum:** the expected-value example lists Salmon at 30% where the stated catch chance is 10% ([[Game Design for Roblox]]). Consider reporting it to the creator-docs repo.

## Needs live verification in Studio

The research environment could not reach Studio, the DevForum or create.roblox.com directly (official docs were read from the `Roblox/creator-docs` repo).

- Whether `ModalManager` should also close on Escape on keyboard, and how that interacts with the Roblox menu ([[Recipe - UI Modal Manager and Button Feedback]]).
- **This game's thumbnail cast:** check the Mother Hen, River Croc and Canyon Scorpion models, egg looks and pets in the live place against [[Free AI Image Tools and Thumbnail Prompts]]. Guard and pet meshes were still placeholders on 2026-10-13.
- Recipe code was type-checked but not run in a live server; run each recipe once in a test place and note it.

## Topics to research further

- **Cross-server trading** with MemoryStore escrow and session-locked DataStores ([[Teleportation and Cross-Server]], [[Data Persistence Guide]]).
- **Server Authority** (beta): follow it to release and update [[Network Ownership and Physics Security]] and [[Remote Security and Anti-Exploit]].
- **Video tutorials** for VFX and R15 animation, with timestamps for the useful parts ([[VFX Production Guide]], [[R15 Blender Animation Pipeline]]).
- **Free AI image tool limits:** re-check every few months; the figures are third-party reports ([[Free AI Image Tools and Thumbnail Prompts]]).
- **DevForum knowledge:** community posts were not reachable during research. Add well-sourced DevForum findings where they extend a note.

## Last Verified

2026-10-14
