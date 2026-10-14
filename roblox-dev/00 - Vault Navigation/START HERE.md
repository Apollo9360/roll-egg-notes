---
title: START HERE
category: Vault Navigation
tags: [start, navigation, index]
status: active
source_quality: Navigation (links to notes whose sources are cited there)
last_verified: 2026-10-14
aliases: [Roblox Development Vault, Vault home, Index]
---

# START HERE

## Summary

This is the **Roblox Development Vault**: verified, practical knowledge for building Roblox games, written for both people and AI agents. It is also this project's shared memory. Look things up here before guessing, and add what you learn ([[CONTRIBUTING TO THE ROBLOX DEVELOPMENT VAULT]]).

- **Official Roblox docs come first.** Every note cites its sources and says when it was last verified.
- **Code is checked.** Every Luau code block was type-checked with luau-lsp against Roblox's API definitions.
- **Community knowledge is labelled.** Hud's Guide (`huds-guide/`) holds measured community data on discovery, ads and monetization; notes here link to it rather than copy it.

## If you are an AI agent

1. Read [[AI Roblox Development Playbook]].
2. Load the context pack for your task:

| Task | Context pack |
|---|---|
| Server code, remotes, services, player data, purchases | [[Roblox Backend Context]] |
| Anything an exploiter could target | [[Security Context]] |
| Screens, HUD, menus, buttons | [[UI Development Context]] |
| Meshes and Blender | [[Blender Modeling Context]] |
| Character or NPC animation | [[R15 Animation Context]] |
| Effects | [[VFX Context]] |
| Frame rate, memory, large worlds | [[Optimization Context]] |

3. Read the project's own notes: `DEV_NOTES.md` (standing rules and latest entries) and `handoffs/`.
4. Before you finish, run the matching checklist from [[MOC - Quick Reference]].

## If you are a person

- **Browse by area:** [[MOC - Engineering]] · [[MOC - Art and Player Experience]] · [[MOC - Game and Business]] · [[MOC - Quick Reference]].
- **The rules that matter most:** [[Roblox Development Golden Rules]].
- **Starting a feature:** [[New Feature Implementation Checklist]]. **Fixing a bug:** [[Bug Investigation Checklist]]. **Shipping:** [[Release Checklist]].
- **Thumbnails and icons for this game:** [[Free AI Image Tools and Thumbnail Prompts]].

## Vault housekeeping

- [[CONTRIBUTING TO THE ROBLOX DEVELOPMENT VAULT]]: how to add or change a note.
- [[VAULT CHANGELOG]]: what changed and when.
- [[Research Backlog]]: open questions and things still to verify.
- [[Source Index]]: every external source the vault cites.

## Note structure

Every topic note has frontmatter (`title`, `category`, `tags`, `status`, `source_quality`, `last_verified`, `aliases`) and these sections, where they apply: Summary, core content, code, common mistakes, Related notes, Sources, Last Verified.

## Last Verified

2026-10-14
