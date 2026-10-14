---
title: UI Development Context
category: Context Packs
tags: [context-pack, ui, ux, mobile, controller, accessibility]
status: active
source_quality: Distilled from the linked vault notes (sources cited there)
last_verified: 2026-10-14
aliases: [UI context]
---

# UI Development Context

## Summary

Load this before building or changing any screen, HUD, menu or button.

## Load these notes, in order

1. [[Roblox UI Engineering Guide]].
2. [[UI Architecture Patterns]] and [[Recipe - UI Modal Manager and Button Feedback]].
3. [[UI Visual Design and Polish]].
4. [[Accessibility and Localization]].
5. [[Input Systems]] and [[Decision - Scale vs Offset]].

## Non-negotiables

- Build UI as authored instances in StarterGui; client controllers bind behaviour to them. Don't generate whole screens from code unless the project already does.
- Scale-based layout with `AnchorPoint`, `UIAspectRatioConstraint` for shapes, size constraints for limits.
- Respect safe areas (`ScreenInsets`) and adapt to `PreferredInput` (touch, gamepad, keyboard and mouse).
- Honour accessibility settings: `PreferredTextSize`, `ReducedMotionEnabled`, `PreferredTransparency`. `TextScaled` text ignores the player's text size.
- Never go below 9 px text. Never use colour alone to carry meaning.
- Every press gets instant feedback. Every modal closes with the gamepad B button.
- Keep text out of images so it can be translated; leave room for longer languages.

## Checklists to run

[[UI Checklist]] · [[Mobile UI Checklist]] · [[Controller UI Checklist]]

## Test

Device Simulator on the smallest phone and a tablet, a gamepad pass, and the largest text size ([[Testing and QA Guide]]).

## Related notes

- [[START HERE]]
- [[AI Roblox Development Playbook]]
- [[Roblox Development Golden Rules]]

## Sources

The notes linked above. Each cites its official sources.

## Last Verified

2026-10-14. Facts here are copied from the linked notes, which cite their sources. If a number here and a note disagree, the note wins; fix this pack.
