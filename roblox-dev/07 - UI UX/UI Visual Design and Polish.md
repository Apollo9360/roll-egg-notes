---
title: UI Visual Design and Polish
category: UI UX
tags: [ui, ux, visual-design, hierarchy, polish, feedback, design-system, hud, shop, inventory]
status: active
source_quality: AUTHORITATIVE principles (Roblox UI/UX design docs) + labelled common practice
last_verified: 2026-10-14
aliases: [Professional Roblox UI Polish, UI design principles, HUD design patterns, Shop UI patterns, Inventory UI patterns, Design system]
---

# UI Visual Design and Polish

## Summary

Polished UI comes from a few fundamentals applied **consistently**:
- **Hierarchy:** the most important thing is the most visible.
- **Attention tools**, used sparingly.
- **A visual language** written down as a style guide or design tokens.
- **Genre conventions.**
- **Instant feedback** on every interaction.

Roblox's own design docs name five pillars: **prioritization, attention, visual language, conventions and consistency**.

## The five pillars

From Roblox's "UI and UX design" doc:
1. **Prioritization**, the hierarchy of information. Ask:
   - What is the player doing now?
   - What must they see or reach?
   - What choices do they face, and what information do those choices need?
   - How often will they use it?

   Show only what matters **in the current context**. For example, swap Sprint/Tackle for Pass/Deke when the player has the ball.
2. **Attention.** Colour (bright = important, muted = secondary), size, negative space, proximity (grouping) and movement. **In moderation**: too much motion and colour overwhelms.
3. **Visual language.** Consistent shapes and colours carry meaning:
   - stat icons share a palette;
   - buttons sit in containers with highlight or shadow so they read as pressable;
   - headers are larger and bolder.

   Document it in a **style guide**.
4. **Conventions.** X closes. Greyed means unavailable. A lock icon means not unlocked yet. "E" prompts mean interact. Play your genre's top games to learn its conventions.
5. **Consistency.** Examples: health is green everywhere; unaffordable prices are always red; close buttons are always the same square red X in the top right.

## Turning it into a design system

| Token group | Define once | Example |
|---|---|---|
| Colours | Brand primary and secondary; currency colours (soft vs premium must differ); rarity colours; success, warning, error; text on dark and light | Cash green, Robux purple, rarity tiers |
| Typography | 1–2 fonts and a size scale (Title, Header, Body, Caption); stroke thickness per size | One bold display font for numbers, one for body |
| Spacing | A rhythm (e.g. multiples of a base unit) for padding and gaps | `UIPadding` values from tokens |
| Shape | Corner radius (or none), stroke widths | Every panel uses the same outline thickness |
| Components | Button (primary, secondary, danger, disabled), panel, card, tab, badge, progress bar, modal | Built once, tagged, styled by StyleSheet |
| Motion | Durations and easings for appear, disappear, press and reward | Fast for feedback, slightly longer for celebrations |

Implement it with **UI Styling tokens and rules** ([[Roblox UI Engineering Guide]]) or a shared `Theme` module. One source of truth beats per-screen tweaking.

## Feedback: why polished UI feels polished

Every interaction answers "did that work?" immediately:
- **Hover and selected:** a slight scale-up or highlight. Gamepad selection must look as obvious as mouse hover.
- **Press:** a slight scale-down, plus a click sound on activation.
- **Result:**
  - success shows a pop, count-up, sound and particles proportional to the value;
  - failure shows a shake or red flash **plus a reason**.
- **Pending:** a spinner or disabled state while the server confirms (purchases, trades).
- **Progress:** bars fill and numbers count up rather than jump.
- **Rewards:** escalate juice with rarity. Common items get a small pop; legendary ones get a screen moment.

Use motion and audio as **information**, not decoration. Respect **Reduce Motion** ([[Accessibility and Localization]]).

## Screen patterns

These are common practice in successful Roblox games. Adapt them; don't copy art.

### HUD

- Keep only the always-relevant items: currency, the core action, objectives.
- Put currency at a **fixed** spot.
- On mobile, put core actions bottom-right within thumb reach.
- Keep the screen centre clear.
- Avoid the top-left and top-centre on phones (top bar and notch); use the safe insets.
- Pop or animate the HUD when values change; that is the feedback loop of a simulator.

### Shop

- Group by category tabs.
- Make **soft and premium currency visually distinct** (colour and icon).
- Highlight value with size and spacing, e.g. a bigger "best value" bundle (Roblox's doc cites Dragon Adventures).
- Show **what you get** with a preview.
- Grey out unaffordable items and show the shortfall.
- Ask for confirmation on expensive purchases.

### Inventory and collection

- Grid with consistent cards: icon, rarity colour edge, count badge.
- Sort and filter.
- Empty-slot silhouettes show what's still missing, which drives collection.
- Selection detail panel.
- Gamepad-navigable grid.

### Modals and popups

- One at a time.
- Dim the background.
- A clear primary action and a clear close.
- Never stack modals or ambush the player with popups during core play.

### Notifications

- Queue them; don't stack them.
- Short, with an icon.
- Placed where they don't cover the core action.
- Auto-dismiss.

### Onboarding and tutorial

- Point to the **one** next action: an arrow or highlight with a minimal text line.
- Fade the rest.
- See [[Game Design for Roblox]] and [[Retention, Onboarding and Playtime]] (Hud's Guide).

## Readability rules

- **Contrast:** outlined text (`UIStroke`) over busy 3D scenes, and dark backing behind long text.
- **Test at the smallest phone size.** If you can't read it there, it's too small.
- **Icons must read at a glance at small size.** Simple silhouettes, consistent style.
- **Cognitive load:** fewer simultaneous choices, progressive disclosure, consistent placement so muscle memory forms.
- **Density:** desktop can show more. Collapse secondary buttons into a menu on small screens (Roblox adaptive design guidelines).

## Common mistakes

- Every element shouting: bright, bouncing, glowing everything.
- Soft and premium currencies that look the same.
- Inconsistent close buttons, button styles or corner radii across screens.
- Feedback missing on failure, or failure messages with no reason.
- Copying another game's proprietary art instead of learning its principles.

## Related notes

- [[Roblox UI Engineering Guide]]
- [[UI Architecture Patterns]]
- [[UI Checklist]]
- [[Recipe - UI Modal Manager and Button Feedback]]
- Example of a measured game-specific style guide: `skills/roll-egg-art-direction/SKILL.md` in this repo

## Sources

- [UI and UX design](https://create.roblox.com/docs/production/game-design/ui-ux-design). AUTHORITATIVE; source of the five pillars and the examples.
- [Adaptive design guidelines](https://create.roblox.com/docs/production/publishing/adaptive-design). AUTHORITATIVE.
- [UI styling (tokens)](https://create.roblox.com/docs/ui/styling). AUTHORITATIVE.
- The screen patterns and motion timings are common practice across popular Roblox titles. COMMUNITY OBSERVATION.

## Last Verified

2026-10-14
