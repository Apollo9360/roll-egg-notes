---
title: Code Review Checklist
category: Checklists
tags: [checklist, code-review]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Review checklist]
---

# Code Review Checklist

- [ ] `--!strict` with no type errors; no deprecated APIs (`wait`, `spawn`, `Humanoid:LoadAnimation`, client `Teleport`).
- [ ] Lives in the right container (server logic not replicated); no work at require time.
- [ ] Remote handlers follow rate → type → value → context → atomic write ([[Remote Security Checklist]]).
- [ ] No yields between check and write on shared state.
- [ ] Every connection, thread and instance has a cleanup path; per-player tables cleared.
- [ ] External calls wrapped in `pcall` with retry or fallback; errors logged with IDs.
- [ ] No per-frame work that could be event-driven; no allocation in hot loops.
- [ ] Single writer for each piece of state; dependency direction respected; no cycles.
- [ ] Client code works with StreamingEnabled (no assumptions that parts exist).
- [ ] Data changes are migration-safe; schema version bumped if needed.
- [ ] Named constants, clear names, comments that explain why.
- [ ] Tested as described in the PR or notes (what was run, on what).

Details: [[Production Luau Coding Standards]]

## Sources

Distilled from vault notes; AUTHORITATIVE sources are cited there.

## Last Verified

2026-10-14
