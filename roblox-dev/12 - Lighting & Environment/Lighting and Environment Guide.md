---
title: Lighting and Environment Guide
category: Lighting & Environment
tags: [lighting, atmosphere, post-processing, shadows, mood, environment-art, day-night]
status: active
source_quality: AUTHORITATIVE (Lighting API and docs) + labelled art practice
last_verified: 2026-10-14
aliases: [Lighting service, LightingStyle, Atmosphere, Bloom, ColorCorrection, Day night cycle, Environment art]
---

# Lighting and Environment Guide

## Summary

Set the **look** with `Lighting.LightingStyle`:
- **Realistic**: the most advanced lighting and shadows;
- **Soft**: a stylized, flat, retro-Roblox look.

`Lighting.PrioritizeLightingQuality` chooses whether shading quality or view distance degrades first on weaker devices. The old **`Lighting.Technology`** property (Voxel, ShadowMap, Future and so on) is **superseded** by these two.

**Build mood with:**
- ambient and outdoor-ambient colour;
- Atmosphere (density, haze, glare);
- Sky;
- a little post-processing (ColorCorrection, Bloom, SunRays, DepthOfField).

**Guide players** with light: bright focal points, landmarks, contrast.

## Lighting properties

Official:

| Group | Properties | Use |
|---|---|---|
| Color | `Ambient` (everywhere, indoors too), `OutdoorAmbient` (outdoors only), `ColorShift_Top` (sun-facing surfaces), `ColorShift_Bottom` (away from the sun) | Overall tint; warm dawn, cool night |
| Intensity | `Brightness`, `ExposureCompensation` | Contrast and exposure |
| Shadows | `GlobalShadows`; `ShadowSoftness` (0 = hard, 1 = soft; Realistic style only) | |
| Appearance | **`LightingStyle`** (Realistic or Soft), **`PrioritizeLightingQuality`** | The artistic intent and the quality trade-off |
| Environment | `ClockTime` / `TimeOfDay` (static unless scripted), `GeographicLatitude` | Sun angle |

**Lighting objects:**
- **`Atmosphere`**: density, offset, colour, decay, glare and haze. Gives distance fog that is better than legacy `Fog*`.
- **`Sky`**: skybox and sun/moon, star count.
- **`Clouds`**: dynamic clouds.
- **Post-processing:** `ColorCorrectionEffect` (brightness, contrast, saturation, tint), `BloomEffect`, `SunRaysEffect`, `DepthOfFieldEffect`, `BlurEffect`.
- **Local lights:** `PointLight`, `SpotLight`, `SurfaceLight`, each with Brightness, Range, Color and Shadows.

## Art practice

Common environment-art practice, not Roblox rules:
- **Pick a style per game, then stay in it.** Stylized games (simulators, tycoons, kid audiences): Soft style, saturated colours, gentle shadows, bright readable spaces. Realistic games (horror, shooters, showcases): Realistic style, controlled exposure, darker ambient, local light pools.
- **Colour scripting.** Give each zone or biome a dominant hue and value. Players learn the world by colour. Shift the mood for events (night, storm, boss).
- **Focal points.** The brightest, highest-contrast spot draws the eye. Put it on the objective (nest, shop, portal). Keep backgrounds lower in contrast and saturation.
- **Landmarks** visible from far away (towers, glowing beacons, unique silhouettes) help navigation without arrows.
- **Readability over realism.** Characters and interactables must separate from the background in both value and colour.
- **Post-processing in moderation.** Light Bloom and ColorCorrection add polish. Heavy DepthOfField or Blur hurts gameplay and readability.
- **Day/night:** script `ClockTime` slowly from the server; it replicates. Or switch preset bundles (Ambient, Atmosphere, ColorCorrection) by tweening on the client, triggered by a server attribute. Keep night **readable**: raise ambient, add rim colour, light the paths.

## Performance

- **Shadows are expensive.** Use `CastShadow = false` on small, distant and moving parts; `Light.Shadows = false` where not needed; limit light range and count. Shadows disable entirely below quality level 4.
- **Partial transparency** stacked on itself causes overdraw.
- **Many local lights** in dense areas: cull them by room or distance.
- **Test** on low graphics quality and a phone. `PrioritizeLightingQuality` decides what degrades first.

See [[Performance and Profiling Guide]].

## Related notes

- [[VFX Production Guide]]
- [[Studio Modeling vs Blender]]
- [[Game Design for Roblox]]

## Sources

- [Global lighting](https://create.roblox.com/docs/environment/lighting), [Atmosphere](https://create.roblox.com/docs/environment/atmosphere), [Post-processing effects](https://create.roblox.com/docs/environment/post-processing-effects), [Light sources](https://create.roblox.com/docs/effects/light-sources). AUTHORITATIVE.
- [Lighting.Technology deprecation message (superseded by LightingStyle + PrioritizeLightingQuality)](https://create.roblox.com/docs/reference/engine/classes/Lighting#Technology). AUTHORITATIVE.
- Art practice: industry-standard environment-art principles. USEFUL.

## Last Verified

2026-10-14
