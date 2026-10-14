---
title: Tooling and Workflow
category: Tooling & Workflow
tags: [tooling, rojo, wally, script-sync, git, stylua, selene, luau-lsp, ci, studio-mcp, version-control]
status: active
source_quality: AUTHORITATIVE (Roblox Script Sync and MCP docs) + HIGH CONFIDENCE (tool repos)
last_verified: 2026-10-14
aliases: [Rojo, Wally, Script Sync, Version control, Git workflow, CI, Studio MCP, External editor]
---

# Tooling and Workflow

## Summary

There are three ways to work, and any can be right:
1. **Studio-only.** Built-in editor, Team Create and version history. Simplest, and best for art-heavy or small teams.
2. **Studio + Script Sync** (built into Studio). Scripts mirror to files on disk for an external editor and Git, while Studio stays the source of truth for everything else. It works with Team Create.
3. **Rojo** (file system as the source of truth). Full Git, code review, CI, and Wally packages. Assets are still usually authored in Studio.

**AI agents** can drive Studio through the built-in **Studio MCP server** ([[AI Roblox Development Playbook]]).

## Option details

| | Studio-only | Script Sync | Rojo |
|---|---|---|---|
| Source of truth | Place file in the cloud | Studio, mirrored to disk for scripts | Files on disk (`*.project.json`) |
| Version control | Studio version history | Git on scripts | Git on everything you put in the project |
| Team Create | Yes | Yes | Partially; teams usually sync code via Git, assets via Studio |
| Packages | Creator Store / Packages | Same | **Wally** (`wally.toml`), plus Creator Store |
| CI (lint, type-check, test) | No | Possible on the synced scripts | Yes |
| Limits | | 10,000 scripts per synced root, 128 roots | |
| Debugger | Studio | Studio (an external debugger can't control Studio) | Studio |

### Script Sync setup

Official:
1. In the Explorer, right-click a folder → **Sync to…** and choose a directory.
2. Open that directory in your editor and add an LSP (luau-lsp).
3. It resumes automatically when you reopen the place.
4. A conflict dialog appears if disk and Studio differ: **Keep Studio** or **Keep Disk**.

Sync **folders** rather than single scripts, so create, rename and delete operations propagate.

### Rojo and Wally

- **Rojo** (repo at **7.7.1**, 2026-10): `default.project.json` maps folders to DataModel paths. `rojo serve` plus the Studio plugin live-syncs; `rojo build` produces place files for CI. It emits a sourcemap for luau-lsp.
- **Wally** (repo at **0.4.0-alpha.1**): a package manager (`wally.toml`, `Packages/`). Shared packages go to ReplicatedStorage; server packages to ServerScriptService or ServerStorage.
- **Toolchain managers** such as Rokit or Aftman pin tool versions per project. These are common community choices.

## Quality tooling

| Tool | Role |
|---|---|
| **luau-lsp** | Type checking and autocomplete with Roblox API definitions and a Rojo sourcemap. This vault's recipe code was checked with luau-lsp 1.70.1. |
| **StyLua** | Formatter |
| **Selene** | Linter (`std = "roblox"`) |
| **TestEZ / Jest-Lua** | Unit tests ([[Testing and QA Guide]]) |
| **Studio Script Analysis** | Built-in lint and type errors |

A typical **CI pipeline** (GitHub Actions) for Rojo projects:
1. StyLua check.
2. Selene.
3. luau-lsp analyze (strict).
4. Unit tests where a runner exists.
5. `rojo build` to make sure the project assembles.

## Studio MCP server (AI tools)

Built into Studio. Enable it under **Assistant → ⋯ → Manage MCP Servers → Enable Studio as MCP server**. Quick connect supports Claude Code, Codex CLI, Cursor, Gemini CLI, VS Code and others.

**Tools by group** (from the official doc):

| Group | Tools |
|---|---|
| Scripts | `script_read`, `multi_edit`, `script_search`, `script_grep` |
| Exploration | `search_game_tree`, `inspect_instance`, `subagent` (explore / playtest) |
| Execution | `execute_luau` (Edit, Client or Server datamodel) |
| Playtesting | `get_studio_state`, `start_stop_play`, `get_console_output`, `screen_capture` |
| Input simulation | `character_navigation`, `user_keyboard_input`, `user_mouse_input` |
| Assets | `search_asset`, `insert_asset`, `upload_image`, `store_image`, `generate_mesh`, `generate_material`, `generate_procedural_model`, `wait_job_finished` |

- Every call takes a `studio_id`; use `list_roblox_studios` to find it.
- **Only connect trusted clients.** MCP clients can read and modify open places.

## Release process

1. Develop in a **test universe** (separate data).
2. Run the QA pass ([[Testing and QA Guide]], [[Release Checklist]]).
3. Publish.
4. Monitor errors, crashes and analytics.
5. Keep the previous version ready to restore from version history.
6. Use **Configs** for kill switches on risky features.

## Related notes

- [[Roblox Project Architecture]]
- [[Production Luau Coding Standards]]
- [[AI Roblox Development Playbook]]
- [[Testing and QA Guide]]

## Sources

- [Script Sync](https://create.roblox.com/docs/scripting/sync), [External tools](https://create.roblox.com/docs/projects/external-tools), [Studio MCP server](https://create.roblox.com/docs/studio/mcp), [Version history](https://create.roblox.com/docs/projects/version-history). AUTHORITATIVE.
- [Rojo](https://github.com/rojo-rbx/rojo) (`Cargo.toml` version 7.7.1, repo 2026-10-05), [Wally](https://github.com/UpliftGames/wally) (0.4.0-alpha.1, repo 2026-10-12), [luau-lsp](https://github.com/JohnnyMorganz/luau-lsp) (1.70.1). HIGH CONFIDENCE.

## Last Verified

2026-10-14
