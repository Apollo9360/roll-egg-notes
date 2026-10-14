---
title: Production Readiness Checklist
category: Checklists
tags: [checklist, release]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Launch readiness]
---

# Production Readiness Checklist

A game is production-ready when:

- [ ] **Security:** all remotes pass the [[Remote Security Checklist]]; subplaces secure; no secrets replicated.
- [ ] **Data:** passes the [[Production Data Persistence Checklist]]; purchases are idempotent.
- [ ] **Stability:** a 1-hour soak with no leaks; join/leave/respawn/teleport stress passes ([[Roblox Stability Engineering]]).
- [ ] **Performance:** baseline phone holds the target FPS and memory ([[Performance Checklist]]).
- [ ] **Cross-platform:** touch, gamepad and keyboard all complete the core loop ([[Controller UI Checklist]], [[Mobile UI Checklist]]).
- [ ] **Onboarding:** first reward within the first minute; onboarding funnel instrumented ([[Analytics and LiveOps Guide]]).
- [ ] **Monetization:** products wired and tested; the shop is clear; no misleading offers ([[Monetization Design]]).
- [ ] **Store page:** thumbnails honestly show the core loop; icon readable at small size ([[Discovery and Marketing Guide]]).
- [ ] **Ops:** test universe, rollback plan, Configs kill switches, error and crash alerts.

## Sources

Distilled from vault notes.

## Last Verified

2026-10-14
