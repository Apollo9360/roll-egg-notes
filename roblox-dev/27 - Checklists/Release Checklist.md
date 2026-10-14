---
title: Release Checklist
category: Checklists
tags: [checklist, release, qa]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Publish checklist, Pre-release]
---

# Release Checklist

- [ ] All changes tested in the **test universe** first (separate data).
- [ ] Server & Clients (2–8 clients) pass: replication, late join, respawn, leave mid-action.
- [ ] Network Simulator bad-mobile pass; real phone and controller pass.
- [ ] Persistence pass: new player, returning player, old schema, outage, shutdown mid-save ([[Production Data Persistence Checklist]]).
- [ ] Purchase pass: each product grants exactly once; rejoin retry works ([[Developer Product Receipt Pipeline]]).
- [ ] Exploit pass on new remotes and prompts ([[Remote Security Checklist]]).
- [ ] Performance soak ([[Performance Checklist]]).
- [ ] Output free of errors and warnings in a fresh session.
- [ ] Analytics events for the new feature are logged (published game only).
- [ ] Configs kill switch for risky features; previous version noted for rollback.
- [ ] Thumbnails and the experience page still match the game (no expectation gap).
- [ ] Unreleased content not shipped in replicated containers.
- [ ] Changelog and dev notes updated; the regression list is re-run.

Details: [[Testing and QA Guide]] · [[Tooling and Workflow]]

## Sources

[Testing modes](https://create.roblox.com/docs/studio/testing-modes), [Version history](https://create.roblox.com/docs/projects/version-history). AUTHORITATIVE.

## Last Verified

2026-10-14
