---
title: New Feature Implementation Checklist
category: Checklists
tags: [checklist, workflow]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Feature checklist]
---

# New Feature Implementation Checklist

1. [ ] **Understand:** read the related vault notes and the existing code; find existing utilities (don't duplicate systems).
2. [ ] **Design:**
   - which loop part it serves;
   - server state and owner;
   - remotes (intent, args, rate);
   - data schema changes;
   - analytics events;
   - UI states (pending, success, fail);
   - the exploit surface ([[Game Design for Roblox]], [[Remote Security Checklist]]).
3. [ ] **Build in small steps:** server logic first (pure modules where possible), then remotes, then client UI and feedback.
4. [ ] **Types and cleanup:** strict types; cleanup objects; PlayerRemoving paths.
5. [ ] **Persistence:** migrate safely; never break old saves.
6. [ ] **Polish:** feedback, VFX, SFX, mobile and gamepad support, accessibility.
7. [ ] **Test:** Server & Clients, exploit pass, phone, performance, analytics firing.
8. [ ] **Document:** project changelog or dev notes; update the vault when you learn something reusable.

## Sources

Distilled from vault notes.

## Last Verified

2026-10-14
