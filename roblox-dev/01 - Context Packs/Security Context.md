---
title: Security Context
category: Context Packs
tags: [context-pack, security, exploits, remotes, ownership]
status: active
source_quality: Distilled from the linked vault notes (sources cited there)
last_verified: 2026-10-14
aliases: [Anti-exploit context, Exploit context]
---

# Security Context

## Summary

Load this before touching remotes, currency, items, physics ownership, teleports or anything an exploiter would target.

## Load these notes, in order

1. [[Remote Security and Anti-Exploit]].
2. [[Network Ownership and Physics Security]].
3. [[Client Server Golden Rules]].
4. [[Teleportation and Cross-Server]] (security settings) and [[Developer Product Receipt Pipeline]] if relevant.

## Non-negotiables

- Assume the client is fully compromised. Any LocalScript, any remote argument and any client-owned physics can be forged.
- Validate every request: rate limit → types → values → context (distance, ownership, cooldown, state).
- Design so cheating is impossible or pointless. Detection is a supplement; prefer quiet, reversible mitigation over visible punishment.
- Never trust physics results from parts a client owns. Keep gameplay-critical assemblies server-owned with `SetNetworkOwner(nil)`.
- `TeleportData` is client-visible and unencrypted: no currency, items or secrets in it.
- Lock non-start places with **Secure within universe only** when only the server should send players there.
- Audit every Toolbox or Creator Store asset for scripts before use. This project has already found a backdoor in a free model (DEV_NOTES, 2026-10-12).

## Checked code

- [[Recipe - Secure Remote Handler]] (Validate + RateLimiter)

## Checklists to run

[[Remote Security Checklist]] · [[Client Server Golden Rules]] · [[Code Review Checklist]]

## Exploit pass before shipping

From [[Testing and QA Guide]]: in a Studio client, fire every remote with wrong types, NaN, inf, huge strings, negative and fractional numbers, tables posing as Instances, and spam at about 100/s; trigger prompts and ClickDetectors from far away. Everything must be rejected with no errors and no state change.

## Related notes

- [[START HERE]]
- [[AI Roblox Development Playbook]]
- [[Roblox Development Golden Rules]]

## Sources

The notes linked above. Each cites its official sources.

## Last Verified

2026-10-14. Facts here are copied from the linked notes, which cite their sources. If a number here and a note disagree, the note wins; fix this pack.
