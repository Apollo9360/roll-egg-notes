---
title: Production Luau Coding Standards
category: Luau & Programming
tags: [standards, code-quality, luau, review, naming, errors, logging]
status: active
source_quality: Synthesized from AUTHORITATIVE docs (Roblox, Luau) plus the community tooling defaults (StyLua, Selene, luau-lsp)
last_verified: 2026-10-14
aliases: [Coding standards, Style guide, Luau conventions, Code quality]
---

# Production Luau Coding Standards

## Summary

Code should be easy for a human **or an AI agent** to read, change safely and review. That means:
- strict types;
- small modules with one owner per responsibility;
- explicit lifetimes and cleanup;
- server authority;
- validated inputs;
- loud failures with context;
- no cleverness without a measured reason.

Recommendations marked **(convention)** are widely used community defaults, not Roblox rules. Pick them once per project, write them down, and enforce them with tools.

## Rules

### Files and modules

1. `--!strict` at the top of every new script and module.
2. **One responsibility per module.** The name says what it owns: `ShopService`, `InventoryController`, `DamageFormula`.
3. **No work at require time.** Expose `start()` (and optionally `init()`); see [[Roblox Project Architecture]].
4. **The public API is the returned table.** Keep helpers `local`.
5. **Export types** for anything other modules consume: `export type Inventory = {...}`.

### Naming (convention)

| Kind | Style | Example |
|---|---|---|
| Services, modules, classes, types | PascalCase | `PlayerData`, `type LootRoll` |
| Locals, functions, methods, table fields | camelCase | `local spawnTime`, `function rollLoot()` |
| Constants | SCREAMING_SNAKE | `MAX_SHOP_DISTANCE` |
| Roblox services | Same as the service | `local Players = game:GetService("Players")` |
| Booleans | Question-like | `isAlive`, `hasLoaded`, `canAfford` |
| Remotes | Verb + noun, written as **intent** | `RequestPurchase`, `RequestFire`, not `GiveCoins` |
| Attributes and tags | PascalCase, documented in one place | `"Door"`, `GetAttribute("OpenTime")` |

### Values and config

6. **No magic numbers.** Named constants at the top of the file, or a shared `Config` module for tunables. Server-sensitive values (prices, drop rates) go in **server-only** config.
7. **Units in names** where they're ambiguous: `durationSeconds`, `rangeStuds`, `speedStudsPerSecond`.

### Errors and logging

8. **Fail loudly with context.** `assert` invariants. `warn(\`[ShopService] purchase failed for {player.UserId}: {reason}\`)`, not a silent `return`.
9. **pcall external calls only**: DataStore, HTTP, Marketplace, Teleport, `require` of optional modules. Don't `pcall` your own logic to hide bugs.
10. **Structured logs for important events**: purchases, trades, bans, data-load failures. Use a consistent prefix and IDs (UserId, PurchaseId, JobId) so logs can be searched. Gate verbose logs behind a `DEBUG` flag.

### Ownership and lifetimes

11. **Every connection has an owner and an end.** Session-long connections live in `start()`. Shorter ones go in a cleanup object ([[Cleanup Pattern (Maid Janitor Trove)]]). Per-player tables are cleared on `PlayerRemoving`.
12. **One writer per piece of state.** Exactly one module may change a value (`PlayerData.addCoins`); everyone else reads it or asks that module.
13. **Event ownership.** A module that fires a signal documents its arguments and when it fires. Listeners never assume the order between different signals.

### Dependencies and the network boundary

14. **Dependency direction:** client → shared ← server. Shared never requires client or server modules. Avoid cycles.
15. **Server authority.** No client-trusted values. Validate remote input with types, values, context and rate ([[Recipe - Secure Remote Handler]]).

### Simplicity and documentation

16. **Defensive, not paranoid.** Validate at boundaries (remotes, DataStore loads, attributes set by the client). Trust your own internal calls.
17. **Avoid premature abstraction.** Build the second use before generalizing. Three similar call sites beat one wrong framework.
18. **Comments explain why**, along with constraints and units, not what the code does. Every non-obvious Roblox quirk you work around gets a one-line comment and a link if possible.
19. **Deprecated APIs are banned.** `wait`, `spawn`, `delay`, `Instance.new(class, parent)` in hot paths, `BodyVelocity` and other legacy body movers (use constraint movers), `Humanoid:LoadAnimation` (use `Animator:LoadAnimation`). luau-lsp flags deprecated APIs.

### Testing

20. **Testable pure logic.** Formulas, loot tables and validation live in pure modules (no Instances) that can be unit-tested; see [[Testing and QA Guide]].

## Tooling (convention)

- **StyLua**: formatter. The default config uses tabs, column 120.
- **Selene**: linter. Use the `roblox` std.
- **luau-lsp**: type checking with Roblox definitions, in your editor or CI.
- **Rojo + Git**: diffs, code review, CI. See [[Tooling and Workflow]].
- **Studio only**: still use `--!strict` and the Script Analysis window.

## Review checklist

See [[Code Review Checklist]].

## Related notes

- [[Luau Language and Performance Guide]]
- [[Roblox Project Architecture]]
- [[Code Review Checklist]]
- [[AI Roblox Development Playbook]]

## Sources

- [Luau type checking](https://luau.org/typecheck) and [Luau lint](https://luau.org/lint). AUTHORITATIVE.
- [Script types and locations](https://create.roblox.com/docs/scripting/locations) (single entry point, start()). AUTHORITATIVE.
- [Roblox deprecated API list](https://create.roblox.com/docs/reference/engine) (marked per member). AUTHORITATIVE.
- StyLua, Selene and luau-lsp project defaults. USEFUL.

## Last Verified

2026-10-14
