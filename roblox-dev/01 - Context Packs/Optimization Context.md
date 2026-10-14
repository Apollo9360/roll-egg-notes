---
title: Optimization Context
category: Context Packs
tags: [context-pack, performance, optimization, memory, streaming]
status: active
source_quality: Distilled from the linked vault notes (sources cited there)
last_verified: 2026-10-14
aliases: [Performance context]
---

# Optimization Context

## Summary

Load this before optimizing, or before adding anything that runs every frame or spawns many instances.

## Load these notes, in order

1. [[Performance and Profiling Guide]].
2. [[StreamingEnabled and Large Worlds]] and [[Decision - When to Use StreamingEnabled]].
3. [[Luau Language and Performance Guide]].
4. [[Roblox Stability Engineering]] (leaks).
5. [[Decision - Polling vs Events]] and [[Decision - When to Use Object Pooling]].

## Non-negotiables

- Measure first, on one baseline low-end device, and keep testing on it.
- Find the bucket first: script compute, memory, physics, rendering (client only) or network. Each has its own tool.
- Frame budget at 60 FPS is 16.67 ms. Server heartbeat is capped at 60.
- No new per-frame loops for convenience; use events. Share one loop where several systems need per-frame work.
- StreamingEnabled is on by default in new places, is set only in Studio, and needs client code that tolerates parts appearing and disappearing.
- Clean up every connection and instance; leaks are the most common live-game failure.

## Checklists to run

[[Performance Checklist]] · [[Production Readiness Checklist]]

## Related notes

- [[START HERE]]
- [[AI Roblox Development Playbook]]
- [[Roblox Development Golden Rules]]

## Sources

The notes linked above. Each cites its official sources.

## Last Verified

2026-10-14. Facts here are copied from the linked notes, which cite their sources. If a number here and a note disagree, the note wins; fix this pack.
