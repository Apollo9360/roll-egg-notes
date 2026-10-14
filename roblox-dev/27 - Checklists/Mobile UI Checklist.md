---
title: Mobile UI Checklist
category: Checklists
tags: [checklist, ui, mobile]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Mobile checklist, Phone UI]
---

# Mobile UI Checklist

- [ ] Tested on the **smallest supported phone** in Device Simulator **and** on a real device, in portrait if supported.
- [ ] Core actions within thumb reach (bottom corners), clear of the jump and thumbstick areas.
- [ ] Touch targets large and spaced apart; no tiny close buttons.
- [ ] Safe areas: CoreUISafeInsets; nothing under the notch or top bar.
- [ ] Text legible without zooming; numbers readable at a glance.
- [ ] Secondary buttons collapse into a menu on small screens (adaptive design).
- [ ] Keyboard hints hidden when `PreferredInput` is touch; touch hints shown instead.
- [ ] Memory: UI images at most 512² (most at most 256²); sprite sheets for icon sets.
- [ ] Frame rate checked with menus open; no per-frame UI churn.
- [ ] Hold interactions show progress; there's an alternative to precise drags.

Details: [[Roblox UI Engineering Guide]] · [[Input Systems]]

## Sources

[Adaptive design](https://create.roblox.com/docs/production/publishing/adaptive-design), [Mobile input](https://create.roblox.com/docs/input/mobile). AUTHORITATIVE.

## Last Verified

2026-10-14
