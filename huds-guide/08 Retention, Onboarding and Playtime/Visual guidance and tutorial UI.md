---
tags: [huds-guide, retention]
---

# Visual guidance and tutorial UI

Up: [[Retention, Onboarding and Playtime]] · Previous: [[Tutorial fundamentals]] · Next: [[Contextual steps and progressive reveal]]

- Visual beats text; kids click Play without reading, so highlight the exact target, such as with a finger-click animation.
- Use highlights, arrows and actions instead of dialogue or paragraphs.
- Many ad players ignore arrows and text; roughly 60% wander briefly and leave.

### Pointing at the target

- Use beams from the player to the destination, in a bright colour, leading to the first object (owner-recommended).
- The beam must appear immediately; a 10-second delay makes players think the game is broken.
- Make arrows animate; avoid pathfinding flicker on the arrows.
- Darken the screen and highlight the one element to press; it raises completion, though it may teach less.
- Make path arrows bigger, brighter (green works), slower and wider, with AlwaysOnTop.
- Use an animated click icon instead of text for the first click step.
- The arrow must match the instruction; mismatches such as "upgrade base" pointing at slots confuse players.
- Point the arrow at an item the player can actually afford; check current money rather than a fixed amount.
- Show the correct action with a ghost avatar.
- Add a forgiveness timer that auto-highlights the target after 30 seconds idle; never let players get stuck.
- Remove arrows and tutorial text as soon as the tutorial ends.

### Text and layout

- Use about 5 words per step; kids read slowly.
- Number steps 1/2/3, show them one by one with big text, and allow Play only after the last.
- White text on a black frame beats coloured text that blends in; drop the "Tutorial:" label.
- Mobile text is often too small or low-contrast; enlarge it.
- Never start a progress bar at 0; start it at 1.
- Praise each completed step, but do not repeat "you did it!" messages.
- Hide all optional UI (user interface) during the tutorial; show only currency.
- Disable purchases players should not make during the tutorial, and add a safe zone.
- Do not make players close GUIs manually during the tutorial.
- Do not overlap tutorial text with background UI or let chat cover it; use IgnoreGuiInset where needed.
- Prevent softlocks on rejoin or refresh, and never require a step the player cannot complete, such as "0/3 rebirths".
- UI animations should take no longer than about 0.2 seconds.

---
Related concepts: [[Onboarding and Tutorials]] · [[Icons and Titles]] · [[Core Loop and Progression]] · [[Updates and Live Ops]]
