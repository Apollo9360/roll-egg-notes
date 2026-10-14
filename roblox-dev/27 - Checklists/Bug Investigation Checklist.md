---
title: Bug Investigation Checklist
category: Checklists
tags: [checklist, debugging]
status: active
source_quality: Distilled from the linked vault notes (AUTHORITATIVE sources cited there)
last_verified: 2026-10-14
aliases: [Bug checklist, Debug checklist]
---

# Bug Investigation Checklist

1. [ ] Write exact repro steps (device, client count, timing). Reproduce it before changing anything.
2. [ ] Classify it: solo Play → logic; Server & Clients only → replication or ownership; live only → data, scale or streaming; mobile only → memory, input or UI scale.
3. [ ] Check the Output and Developer Console (client **and** server logs) for the first error, not the last.
4. [ ] Find which side is wrong (client/server toggle). Log both sides with a shared request ID.
5. [ ] Check lifetimes: still parented? respawned? streamed out? player left?
6. [ ] Check yields and order: deferred events, a yield between check and write, a handler connected late.
7. [ ] Fix the cause; no `task.wait` band-aids. Add a guard plus a log for the impossible state.
8. [ ] Verify with the same repro; add it to the regression list; note the cause in the changelog.

Details: [[Debugging Guide]] · [[Roblox Stability Engineering]]

## Sources

[Debugging](https://create.roblox.com/docs/studio/debugging). AUTHORITATIVE.

## Last Verified

2026-10-14
