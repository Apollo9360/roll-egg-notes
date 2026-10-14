---
title: Accessibility and Localization
category: UI UX
tags: [accessibility, localization, translation, text-size, reduced-motion, readability, autolocalize]
status: active
source_quality: AUTHORITATIVE
last_verified: 2026-10-14
aliases: [Accessibility, Localization, Translation, AutoLocalize, Reduce Motion, Text size, Colorblind]
---

# Accessibility and Localization

## Summary

- **Honour the player's Roblox accessibility settings:**
  - Text Size (`GuiService.PreferredTextSize`);
  - Reduce Motion (`GuiService.ReducedMotionEnabled`);
  - Background Transparency (`GuiService.PreferredTransparency`).
- **Design text to grow:** AutomaticSize, wrapping, no `TextScaled` on body text.
- **Localization:** Roblox auto-translates game text for players who enable Automatic Translations. Set source and supported languages in Creator Hub. Turn off `AutoLocalize` on text that must never be translated (names, signs). Leave room for **text expansion**.

## Accessibility settings

All official:

| Setting | API | Implementation |
|---|---|---|
| Text Size | `GuiService.PreferredTextSize` (Medium default → Large, Larger, Largest) | `UITextSizeConstraint` caps it; `TextScaled` text **ignores** it; `AutomaticSize` grows with it; `TextWrapped` wraps more; `TextService:GetTextSize` honours it |
| Reduce Motion | `GuiService.ReducedMotionEnabled` | Tween time 0; skip camera shake, bounces and parallax |
| Background Transparency | `GuiService.PreferredTransparency` | `element.BackgroundTransparency = default * GuiService.PreferredTransparency`, updated on its change signal |

### Design practice

Practice aligned with the official adaptive design guidelines:
- **Never use colour alone** to carry meaning. Add an icon, a label or a shape: rarity frames with distinct borders, team markers with symbols.
- **Contrast:** outlined text over 3D scenes, solid backings behind long text.
- **Minimum text size:** don't go below **9 px** (official guidance on `MinTextSize`). Test on the smallest phone.
- **Input:** every action reachable by touch, gamepad and keyboard; big touch targets ([[Input Systems]]).
- **Audio cues** have visual equivalents (hit markers, subtitles for important voice lines).
- **Younger players:** short words, icons, and showing rather than telling.

## Localization

From the official docs:
- **Automatic translation.** Players with **Automatic Translations** on get game names, descriptions and in-game text translated automatically. **Manual translations always win** over automatic ones.
- **Setup:** Creator Hub → your game → **Audience → Localization**. Set the **source language** and **supported languages**.
- **Automatic Text Capture (ATC)** collects UI strings players encounter into the localization table. It can take days, and only captures objects with `AutoLocalize = true`. Add strings manually for content rarely seen in play (deep dialogue trees).
- **Dynamic text:** strings built in code ("You have 5 coins") need **parameterized** translation entries via localization scripting (Translator objects from LocalizationService) so word order can change per language.
- **Opt out:** set `AutoLocalize = false` on names, codes and proper nouns.

### Design for translation

- **Text expansion.** Some languages run much longer than English. Use `AutomaticSize`, wrapping and flexible layouts, and never fit text exactly to an English-sized box.
- **No text baked into images.** Use TextLabels over images so text can be translated.
- **Numbers and dates:** format them in code with locale awareness where it matters.

## Related notes

- [[Roblox UI Engineering Guide]]
- [[UI Visual Design and Polish]]
- [[Input Systems]]
- [[Mobile UI Checklist]]

## Sources

- [Accessibility](https://create.roblox.com/docs/production/publishing/accessibility), [Adaptive design](https://create.roblox.com/docs/production/publishing/adaptive-design), [Size modifiers (MinTextSize)](https://create.roblox.com/docs/ui/size-modifiers), [Localization](https://create.roblox.com/docs/production/localization), [Automatic translations](https://create.roblox.com/docs/production/localization/automatic-translations), [Translate dynamic content](https://create.roblox.com/docs/production/localization/translate-dynamic-content). AUTHORITATIVE.

## Last Verified

2026-10-14
