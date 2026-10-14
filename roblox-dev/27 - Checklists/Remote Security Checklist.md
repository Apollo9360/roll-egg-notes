---
title: Remote Security Checklist
category: Checklists
tags: [checklist, security]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Security checklist]
---

# Remote Security Checklist

For every remote, ProximityPrompt, ClickDetector or DragDetector handler:

- [ ] **Rate limited per player** on the server (token bucket); per-player state cleared on PlayerRemoving.
- [ ] **Types checked with `typeof`.** Instance arguments: `typeof == "Instance"`, plus `IsA`, plus `IsDescendantOf(expectedFolder)`.
- [ ] **Numbers:** `math.isfinite`, ranges, integers where needed; no negative quantities.
- [ ] **Strings:** length cap, and `utf8.len` validity if they will be saved.
- [ ] **Tables:** size cap, no client-chosen arbitrary keys, no NaN or nil keys.
- [ ] **IDs from an allow-list**, not arbitrary paths or instances to modify.
- [ ] **Context checked on the server:** alive, in range (server positions), owns the item or key, off cooldown, correct team.
- [ ] **Check-then-write with no yield between.** Server-side prices and stats only.
- [ ] **Prompts:** handle only `Triggered` for actions; re-check `Enabled` and distance; enforce hold time on the server; anchor prompt parts or set their network owner.
- [ ] **Relays to other clients are validated and rate-limited** before broadcasting.
- [ ] **No server logic or secrets** in replicated ModuleScripts.
- [ ] **Robux:** grant only in `ProcessReceipt` ([[Developer Product Receipt Pipeline]]).
- [ ] **Exploit test done:** wrong types, NaN, inf, huge strings, negative and fractional numbers, tables posing as Instances, 100/s spam, far-away prompts.
- [ ] **Rejections are logged** with UserId (feeding a suspicion score, not instant bans).

Details: [[Remote Security and Anti-Exploit]] · [[Recipe - Secure Remote Handler]]

## Sources

[Securing the client-server boundary](https://create.roblox.com/docs/scripting/security/client-server-boundary). AUTHORITATIVE.

## Last Verified

2026-10-14
