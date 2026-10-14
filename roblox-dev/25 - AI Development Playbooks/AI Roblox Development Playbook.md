---
title: AI Roblox Development Playbook
category: AI Development Playbooks
tags: [ai, agents, claude, codex, workflow, studio-mcp, playbook, memory]
status: active
source_quality: AUTHORITATIVE (Studio MCP docs) + this team's working conventions
last_verified: 2026-10-14
aliases: [AI playbook, Agent workflow, How AI should work on Roblox, Claude Roblox workflow, Codex Roblox workflow]
---

# AI Roblox Development Playbook

## Summary

**Read before you write, and verify before you claim.** An AI agent working on a Roblox project should:
1. Load the right **context pack**.
2. **Inspect the real project**: Studio via MCP, or files via Rojo or Script Sync.
3. **Find existing systems and conventions.**
4. Make **small, typed, server-authoritative changes**.
5. **Verify them**: type-check, run, and screenshot or log.
6. **Record what it learned** in the project's notes and in this vault.

**This vault is the shared memory.** Don't keep personal memory files in the repo. Durable knowledge goes into the vault; project-specific decisions go into the project's dev notes.

## 0. Before starting

- **Load context:**
  - [[START HERE]];
  - the relevant **Context Pack** (e.g. [[Security Context]], [[UI Development Context]]);
  - the project's own notes (in this repo: `DEV_NOTES.md` standing rules, the latest entries and `handoffs/`).
- **Clarify the goal and the definition of done.** Ask the human only for decisions that are genuinely theirs: design choices, money, publishing, anything destructive. Infer everything else from the code and the notes.
- **Know what you may not do.** Respect the project's standing rules. Never start actions with external effects (publishing, purchases, Creator Hub changes, deleting data) without explicit approval.

## 1. Inspect the project

**With the Studio MCP server** (built into Studio, enabled under Assistant → Manage MCP Servers):
- `list_roblox_studios` → pick the `studio_id`.
- `search_game_tree` / `inspect_instance` to map containers, services, remotes, tags and attributes.
- `script_search` / `script_grep` / `script_read` to read code.
- `get_console_output` for errors; `get_studio_state` before acting.
- `screen_capture` to *see* UI and world state. Some UI, such as ProximityPrompts, may not render in edit-mode captures.

**With files** (Rojo or Script Sync): read the project file or sync roots, then grep for services, remotes and the bootstrap.

**Map these before changing anything:**
- the bootstrap or entry points;
- where authoritative state lives (which service owns money and inventory);
- the remote list and its validation;
- the data module and schema;
- UI structure;
- naming conventions;
- existing utilities: cleanup, signals, validation, config.

## 2. Plan

- **Reuse before you add.** Search for an existing helper first; duplicate systems are a top source of bugs.
- **Respect the architecture** ([[Roblox Project Architecture]]): server services own state, client controllers present, shared modules hold pure logic.
- **Respect the boundary** ([[Client Server Golden Rules]]): decide server vs client per piece of logic before writing code.
- **Check the security surface** ([[Remote Security Checklist]]) and the data impact ([[Production Data Persistence Checklist]]) for anything that touches remotes, currency, items or saves.
- **Prefer small, verifiable increments** over large refactors. Refactor only with a reason, and in steps that each work.

## 3. Implement

- `--!strict` Luau; follow [[Production Luau Coding Standards]] and the project's existing style.
- **Build real instances where the project expects them** (UI in StarterGui, world objects in Workspace). Don't generate everything from scripts unless that is the project's convention.
- **Server-validated remotes**, with recipes from `28 - Recipes`.
- **Cleanup for everything you connect or create.**
- **Data changes are migration-safe**: bump the schema version and add migrations.
- **Never** paste secrets, never copy code or assets you don't have rights to, and audit Toolbox assets for scripts before use.

### Editing live Studio places safely

- **`execute_luau` in Edit mode changes the place immediately.** Prefer `multi_edit` for scripts. Use `execute_luau` for inspection or deliberate instance edits.
- **Be careful with `require` in `execute_luau`.** Edit-mode `require` caches modules, so you may get stale code. Clone the module to load the new version, and never assume it reflects live play state.
- **Don't start or stop playtests unless the human allows it.** If you do, make sure no other agent is mid-task.
- **After edits:** read back what changed, check the Output, and capture a screenshot for anything visual.

## 4. Verify

- **Type-check:** Studio Script Analysis, or luau-lsp with Roblox definitions ([[Tooling and Workflow]]).
- **Run it:**
  - Server & Clients for multiplayer features;
  - Network Simulator for network-sensitive features;
  - Device Simulator for UI;
  - the exploit pass for remotes ([[Testing and QA Guide]]).
- **Check performance:** no new per-frame work, no leaks ([[Performance Checklist]]).
- **Report honestly:**
  - what was tested, and how;
  - what wasn't tested, and why (e.g. "prompt UI doesn't render in edit mode; check in a playtest");
  - known risks.

## 5. Record

- **Project log:** add a dated entry to the project's dev notes (what changed, where, why, and what wasn't verified).
- **Hand-offs:** for multi-session work, write a handoff in `handoffs/` with goals, state, open questions and exact paths.
- **The vault:** when you learn something reusable (an engine quirk, a verified API behaviour, a better pattern), add or extend the right vault note, following [[CONTRIBUTING TO THE ROBLOX DEVELOPMENT VAULT]]. Then log structural changes in [[VAULT CHANGELOG]], and put open questions in [[Research Backlog]].

## Anti-patterns

- Coding before reading the existing systems, which produces duplicate managers, remotes and data paths.
- Trusting the client "temporarily".
- Claiming "done" without running it; claiming APIs from memory without checking the docs.
- Huge, sweeping refactors mixed with feature work.
- Silent changes to data schemas.
- Creating per-frame loops for convenience.
- Copying another game's assets or code (even from a reference decompile) into the project.
- Storing personal preferences or chat memory in the shared repo instead of the vault or project notes.

## Prompt template

For a human starting an agent on a Roblox task:

```text
Project: <name>. Read roblox-dev/00 - Vault Navigation/START HERE.md, then the context pack for
<area>, then DEV_NOTES.md (standing rules + latest entries).
Task: <goal>. Done means: <observable result>.
Constraints: server-authoritative; strict Luau; reuse existing systems; no publishing.
Verify with: <tests / screenshots / exploit pass>. Log the change in DEV_NOTES.md and add any
reusable lesson to the vault.
```

## Related notes

- [[START HERE]]
- [[Roblox Development Golden Rules]]
- [[New Feature Implementation Checklist]]
- [[Bug Investigation Checklist]]
- [[Tooling and Workflow]]

## Sources

- [Studio MCP server](https://create.roblox.com/docs/studio/mcp) (tool list and `studio_id` model). AUTHORITATIVE.
- This repo's `DEV_NOTES.md` standing rules (edit-mode `require` caching; screen captures not showing ProximityPrompts). PROJECT-VERIFIED.
- The vault notes linked above.

## Last Verified

2026-10-14
