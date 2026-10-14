---
name: roll-egg-art-direction
description: Art direction for the Roblox game "Roll and Steal an Egg!" (place 122907972658396). Use BEFORE building or changing anything visual in that game — HUD, menus, buttons, popups, BillboardGuis, map parts, decor, pets, eggs, guards, particles/VFX — so every build matches the Steal an Egg look. Also use when the user says something "looks bad", "low effort", "doesn't match", or asks for polish/restyle.
---

# Roll and Steal an Egg! — Art Direction

Target look: **Steal an Egg**. Chunky toy-brick world, very saturated colours, panel-less outlined HUD numbers, dark patterned menus with thick black outlines, voxel pets/eggs that glow and sparkle by rarity. Numeric values, colours and image ids below were measured from the Steal an Egg decomp (2026-10-12) unless marked *(ours)* — those are our own design choices. Use the values, don't eyeball.

## Workflow (every visual task)

1. **Look first.** `screen_capture` our game at the spot you'll change. If the decomp Studio ("test", placeId 123277966547265) is open, capture the matching spot there with the same framing. Screenshots are allowed during the user's play session; never start a playtest.
2. **Build to the recipes below.** Real instances in StarterGui/Workspace; controllers only bind. Shared image ids go in `Shared.Config.Icons`.
3. **Check after.** Capture again from the same camera. Run the checklist at the bottom. Fix and re-capture until it passes, then show the user before/after.
4. In the decomp place you may toggle ScreenGui `Enabled` / frame `Visible` and temporarily clone models for photos — record original state in attributes and restore it, destroy temp clones. Never copy its scripts or models into our game. Its UI image ids and numeric style values are fine to reuse (user: reference UI images are open-use).
5. Log the change in DEV_NOTES.md. Don't rebuild or move the map (home is world +Z); restyle World parts in place.

## Type

- Font: **Rubik** (`rbxassetid://12187365977`), Bold. HUD numbers and money: **Bold Italic**. Body/labels: Bold. Never FredokaOne, Gotham, Arial or Legacy fonts for new UI.
- `TextScaled = true` with `UITextSizeConstraint` on small labels.
- Every text gets a black `UIStroke` (Contextual): 2.0 on HUD/body text, 3.0–3.3 on titles, Round join on titles.

## HUD (bottom-left stats)

No background panel. Each number is a 3-layer stacked label:
- Base `TextLabel`: text black, `UIStroke` 2.0 black Bevel join, transparent bg, Rubik Bold Italic.
- Child `Shadow`: same text, the colour, Size {1,0},{1,0}, Position Y ≈ 0.46 (base at 0.5) → shifted up for a 3D extrude.
- Optional child `White` highlight layer at the same offset.
- Colours: money `38,255,0`; speed `0,221,255`; boost/luck text `242,255,0`.
- Icon sits left of the number (shoe for speed, cash stack for money), same height as the text.
- Pop/count-up animations stay (UIFx), they just act on these labels.

## Side buttons (left/right menu tiles)

Square tile, no rounded pill:
- Fill frame in the button colour: shop green `20,202,6`, index cyan `0,179,229`, red `202,13,0`, yellow `223,223,5`.
- Overlay `InletTexture` ImageLabel, image `rbxassetid://103855926983380`, ScaleType Tile, ImageTransparency 0.78.
- Overlay `BevelEffect` ImageLabel, image `rbxassetid://74603642649742`, ScaleType Slice, ImageTransparency 0.08.
- Outer `UIStroke` 1.0 black.
- Big icon (Vector) filling most of the tile, label underneath: white Rubik Bold, `UIStroke` 1.5 in a **darker shade of the fill** (green `21,139,0`, cyan `0,105,131`, red `139,0,0`).
- Notification badge: round, gradient `255,0,4 → 255,39,125` (rot -90), white count, black stroke 1.

## Menu panels (Shop, Eggs, Pets, Index, Wheel shop, popups)

Panel body (bottom to top):
1. `DropShadow` ImageLabel `rbxassetid://96735047582734`, Slice, black, ImageTransparency 0.87, slightly larger than the panel.
2. `Background` Frame, transparent, with `UIStroke` **4.9 black**, Round join.
3. `CanvasGroup` holding a Stretch ImageLabel `rbxassetid://91160167769370` (the dark navy fill).
4. `InletTexture` Tile `rbxassetid://103855926983380`, ImageTransparency 0.68.
5. `Pattern` Tile `rbxassetid://131176354845909`, TileSize {0.2,0},{0.3,0}, ImageTransparency 0.84 (diamond pattern).
6. `BevelEffect` Slice `rbxassetid://74603642649742`, ImageTransparency 0.78–0.80.

Title: white Rubik Bold, `UIStroke` 3.0–3.3 black Round, **half outside the panel's top-left** (Position Y ≈ -0.06), with the menu's icon just left of it. Optional title gradient (Shop: `0,214,0 → 0,255,17`).

Header-bar variant (shop-style menus): a bar across the top filled with a multi-stop gradient (yellow: `255,170,1 / 255,255,0 / 255,200,0 / 255,255,0 / 255,255,127 / 255,255,0 / 255,217,0 / 255,255,0 / 255,170,0`), InletTexture 0.81, Bevel 0, stroke 4.9 black, title inside it.

**Never rotate a panel or anything that clips.** Roblox turns clipping off for every descendant of a GuiObject whose Rotation ≠ 0 (even 0.0001°). This applies to ClipsDescendants and ScrollingFrames alike. Rotate leaf elements only (stamps, icons, rows without clipping inside them). Menus rotate only through ModalFx's CanvasGroup rotor (`s.rotor`), never the modal itself. Full-screen layers go in `s.home`, not `modal.Parent`.

Close button: square, top-right corner overlapping the border.
- Image `rbxassetid://112096965608582` (Fit) with `UIGradient` rot 90 `255,9,0 → 209,0,7`, Pattern tile overlay 0.75.
- White "X" Rubik Bold, `UIStroke` 2.1 black.
- Red glow: Slice `96735047582734`, colour `255,0,4`, transparency 0.8.

Item cards: grey gradient fill rot 100 `188,188,188 → 107,107,107`, `UIStroke` 2.8 black, white title + rarity text (stroke 2), stock chip in a dark rounded box.
Buy buttons: cash `0,255,21`, Robux purple `183,0,255` (must look different from cash), white price text with black stroke, black outline.
Progress bars: thick black outline, bright fill (bright green, or cyan→blue gradient; exact colours not measured), centred white count with black stroke.

## World

- **Floors:** Plastic + MaterialVariant **`Studs`** (already in our MaterialService), plus a top-face `Texture` `rbxassetid://6372755229`, Color3 black, Transparency 0.8, StudsPerTileU/V 8 (the checker). Grass `63,165,0`; desert `171,126,44`; volcano `49,43,39`. Saturated, never pastel.
- **Walls:** tall (~37 studs) corridor walls along each zone, SmoothPlastic + MaterialVariant **`2022 Stud`**, Texture `rbxassetid://6905003203` Transparency 0.9, 8 studs/tile, lime trim on top. Terracotta near home, `207,160,65` sand, `67,49,36` volcano.
- **Lighting:** leave as is — it already equals theirs.
- **Decor:** few, large, chunky voxel props (Toolbox, audited in Quarantine). Neon accents for drama (lava cracks, glowing crystals, fire). No confetti fields of tiny parts.
- **Beams/markers:** thin and see-through; never hide the guard or nest.

## Pets, eggs, guards

- Voxel style: Plastic + `Studs` variant body, Neon for glowing details. No plain box or plain sphere placeholders.
- Rarity aura = Attachment with these emitters (tint per rarity, all LockedToPart, FacingCamera, LightEmission 1):
  - `Sparkles` tex `rbxassetid://1084970835`, rate 10, life 1.5–2, size 0.35→0, speed 10, spread 360, RotSpeed 5.
  - `Circles` tex `rbxassetid://1084982817`, rate 8, life 1.1, size 0.12→0, speed 20, spread 360.
  - `Flare` tex `rbxassetid://867619398`, rate 1, life 1, size 7, Brightness 0.2, ZOffset 4.
  - `Rays` tex `rbxassetid://1053548563`, rate 8, life 1, size 0→10 (ease-out), transparency 0.4→1, rotation 0..360.
  - `Sunburst` tex `rbxassetid://1084975295`, rate 10, life 1, size 3.3→7.4→7.2, transparency 1→0→1, RotSpeed 22.
  - *(ours)* Common: none. Uncommon/Rare: Sparkles + Circles. Epic: + Flare. Legendary/Mythic: + Rays + Sunburst. Secret: everything, bigger and faster. Variant eggs (Golden, Rainbow…) tint the aura to the variant.
- Eggs keep their pattern/variant colour but use the studded material and get the aura.

## Done checklist (compare screenshots)

- [ ] Any FredokaOne / Gotham / Arial left in what I touched? → Rubik.
- [ ] Every text has a black stroke; HUD numbers have the coloured shadow layer, no backing panel.
- [ ] Panels: 4.9 black outline, dark patterned fill, title overhanging top-left, square red X.
- [ ] Buttons: square tile, inlet + bevel overlays, darker-hue label stroke.
- [ ] World surfaces use `Studs` / `2022 Stud` + checker texture; colours saturated.
- [ ] Rare things sparkle; nothing important is a plain placeholder shape.
- [ ] Phone width: text still readable, buttons ≥ 44 px, nothing overlapping.
- [ ] Before/after screenshots shown to the user; nothing was playtested.
