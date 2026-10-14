---
title: Free AI Image Tools and Thumbnail Prompts
category: Discovery & Marketing
tags: [thumbnails, icons, ai-images, prompts, discovery]
status: active
source_quality: mixed (official Roblox docs for specs; third-party reports for tool limits)
last_verified: 2026-10-14
aliases: [Thumbnail Prompts, Free Image Generators, AI Thumbnail Workflow]
---

# Free AI Image Tools and Thumbnail Prompts

## Summary

How to make Roblox thumbnails, icons and item icons for free with AI tools, plus copy-paste prompts written for **Roll and Steal an Egg!**

- Use free generators that accept **reference images**: Gemini (Nano Banana 2), Google Flow and ChatGPT.
- Do layout and text yourself in **Photopea** or **Canva**.
- **Every prompt sells the core fantasy** so the game is clear without its title: steal an egg, roll it home as it grows, and escape the guard ([[Show the core fantasy and loop]]).
- **Every prompt uses the game's real cast:** the Mother Hen, River Croc and Canyon Scorpion guards, zone eggs, real pets, the gold SAFE ZONE line and the clover luck tag ([[#Our game's look and cast]]).
- Test 10–25 distinct thumbnails in ads before choosing the 2–5 for the home page.

The strategy is in [[Thumbnails and Icons]] (Hud's Guide, chapter 07). This note covers tools and prompts only.

> [!warning] Free limits change often
> Every free limit below comes from third-party reports from 2026. The sources disagree, and the providers adjust limits to demand. Check the tool itself before planning a batch around a number.

## Roblox rules the images must meet

These come from the official Roblox docs (`production/publishing/thumbnails.md` and `experience-icons.md` in the creator-docs repo, as of 2026-10-13).

| Item | Requirement |
|---|---|
| Thumbnail size | **16:9**, ideally **1920×1080**. Roblox stretches anything else to 16:9. Formats: jpg, gif, png, tga, bmp. |
| Home-page uploads | Keep each file **under 3 MB** and at most 1920×1080. |
| Home-page personalization | Starts once **2 or more** thumbnails are active; up to 5 can be active. Roblox shows each player the thumbnail with the best qualified play-through rate for their group. Keep several active rather than picking one winner. |
| Bottom edge | Keep important text and subjects **off the bottom**, where player count and other details can cover it. |
| Detail page | Up to **10** images or videos. The Home Page and Experience Detail Page tabs are separate, so upload to both. |
| Icon | **Square, 512×512**. It can shrink to about **150×150**, so check it at that size. |
| Videos | Must be authentic gameplay: no voice-over, no lyrics, no real-world footage, no "50% off"-style claims. 3 uploads a month. |
| Moderation | Every upload is moderated against the Community Rules. |
| Free camera | Press **Shift+P** in a solo playtest for freecam, which is good for capturing clean scenes to use as AI references. |

Community-reported, not in the docs:
- A Robux logo on a thumbnail reportedly got a game banned.
- Don't put the game title or a logo on the thumbnail.

## Free platforms compared

| Tool | Free allowance (reported) | Best for | Reference images | Watch out for |
|---|---|---|---|---|
| **Google Gemini app** (Nano Banana 2) | ~20 images/day at 1K; the Pro model is ~2/day, watermarked | Reskinning from references, consistent characters, edits | Yes, several | Limits vary with load. Upscale for 1920×1080. |
| **[Google Flow](https://labs.google/fx/tools/flow)** | Image generation reportedly uses no credits; reported throttle around 100/day | **Bulk batches**: 4 images per run | Yes | Video costs credits. Reports conflict. |
| **ChatGPT** (GPT Image 2) | ~2–3 images per rolling 24 h (unofficial) | Best text in images, icons, polished compositions | Yes | Small free cap; save it for finals. |
| **Microsoft Bing Image Creator / Copilot** | ~15 fast/day, then unlimited slow (5–15 min waits) | Free volume when Google's limits run out | Limited | Free output is reportedly **non-commercial**. Read the terms before using it in ads. |
| **Meta AI** | Large (reported 500+/day) | Fast idea sketches | Limited | Quality varies. |
| **Ideogram** | About 10 slow generations (per day or per week; reports differ) | Text-heavy designs, logos | Some | Free images are public. |
| **Recraft** | 30–50 credits/day (raster 1, vector 2) | **Vector icons**, consistent icon sets | Style references | Free tier: public, **no commercial license**. |
| **Leonardo** | ~150 tokens/day | Model variety, game-asset styles | Yes | Credits go quickly at high resolution. |
| **Krea** | ~100 compute units/day | Real-time sketch to image | Yes | Vendor-reported figure. |
| **Adobe Firefly** | Small daily allowance | Commercially safe training data | Yes | Very limited. |
| **Local: Stable Diffusion / Flux in ComfyUI** | Unlimited | Unlimited test batches if you have a GPU | Yes (IP-Adapter, ControlNet) | Needs about 8 GB+ VRAM and setup time; check each model's license. |

**Free finishing tools:**
- **Photopea**: free web Photoshop for strokes, text and compositing.
- **Canva free** and **Figma**: layouts.
- **remove.bg**: cut-outs.
- **Upscayl**: free, open-source upscaler that runs locally.
- **[qptr.io](https://qptr.io)**: previews a thumbnail in a home-page grid (no data).

> [!info] Commercial use
> A Roblox game with paid ads and Robux sales is commercial. Avoid tools whose free tier is non-commercial (reportedly Bing and Recraft) for anything that ships, or check their current terms first. Gemini, Flow and ChatGPT outputs are generally usable commercially under their terms. Re-check, because terms change.

## Recommended free setup

1. **References first.** In Studio, set up your real scene: player avatar, guard, giant egg, plot, wheel. Use freecam (Shift+P) and take 3–5 clean screenshots. Also grab 3–5 top thumbnails from your genre (Steal an Egg, Steal a Brainrot, Grow a Garden) **for layout only**; never copy their art.
2. **Bulk drafts in Google Flow** (4 per run) or the **Gemini app**. Attach your screenshots and one genre reference, then use the concept prompts below. Aim for 20–30 drafts.
3. **Fix up and finals** in Gemini (edits) or **ChatGPT** (best text and finish) for the best 10–12.
4. **Text and strokes in Photopea**:
   - Use **Rubik Bold Italic**, the game's font. Fredoka One is the genre default.
   - White fill, black outline 8–12 px at 1920 wide.
   - Add a thin white inner stroke inside a black outer stroke on the main subject.
5. **Upscale** to 1920×1080 with Upscayl and export under 3 MB (JPG at about 90% quality).
6. **Check at small size.** Shrink to 300 px wide, then 150 px for icons. Can you tell what the game is within about 100 px?
7. **Test.** Run 10–25 distinct concepts in an ad campaign and judge each after about 10k+ impressions. A 3% CTR is the common floor. Put winners that honestly show the core loop on the home page (2–5 active).

## Prompt formula

`[Format/shot] + [subject doing the core action] + [the hook/goal] + [emotion] + [setting, simplified] + [style block] + [composition rules] + [what to leave out]`

### Style block for every prompt

Paste this after each concept so every image matches the game:

```
Style: Roblox game thumbnail, bright saturated toy-brick world, chunky studded plastic floors
with a faint checker pattern, blocky Minecraft-like trees, chunky low-poly creatures and glossy
eggs that glow by rarity, cartoon 3D render with soft lighting and soft shadows, bold clean
shapes, playful cartoon proportions, high contrast between subject and a simple background,
thick dark outlines around main subjects, 16:9 landscape. Keep the bottom 15% of the image free
of important details. No text, no logos, no watermark, no UI, no Robux symbol.
```

The floors, trees and colours match the current build (see [[#Our game's look and cast]]).

### Avatar block

Use this for kid-relatable avatars:

```
The player is a classic Roblox avatar: blocky body, "bacon hair" brown hairstyle, simple smiling
cartoon face, plain t-shirt; proportions exactly like a standard Roblox character, not a realistic
human. Clean hands with no extra fingers.
```

Generate **without text**, then add text yourself. Ask for "no text"; AI lettering is unreliable even when it looks fine at full size.

## Sell the core fantasy first

From [[Show the core fantasy and loop]] (Hud's Guide): the thumbnail must explain the game in one image, readable within about 100 px, so a kid knows exactly what they would be doing, **without the title**. It shows the action rather than the object, the core loop rather than side mechanics, the player **winning**, and the key item made special.

### What Roll and Steal an Egg! is, in one picture

Taken from this repo's `DEV_NOTES.md`:
1. **Steal:** grab an egg from a guarded nest in a biome (Meadow, then Riverbank, then Canyon, and more as Speed unlocks them).
2. **Roll it home:** the egg **grows with every step you roll it**, like a snowball. Bigger eggs carry more luck (the green clover x-multiplier), so **bigger egg = rarer pet**.
3. **Escape:** the nest's guard chases you until you cross the gold **SAFE ZONE** line.
4. **Hatch:** place the egg on your plot. After its timer, it hatches a pet that earns cash per second.

**The hook versus other "steal" games is rolling an egg that keeps getting bigger.** Every concept below shows three things:
- the **roll**: the egg on the ground, pushed and rolling, never held in the arms;
- the **size**: the egg bigger than the player, the payoff;
- the **steal tension**: a guard chasing, or the nest left behind.

**Leave these off as the main subject.** They are side mechanics and don't explain the game:
- the hamster wheel;
- skip and hatch timers;
- plot layout;
- Speed numbers;
- wheel tiers.

**Honesty check:** use the real look of your eggs, guards and pets.
- Attach Studio screenshots of the actual guard model, a rolled egg and an Owl pet.
- AI-invented monsters that aren't in the game cause the expectation gap described in [[Honesty vs clickbait]].
- Confirm with one screenshot that the rolled egg really sits on the ground in front of the player, and adjust "pushing" to match the in-game pose.

## Our game's look and cast

Use these names, colours and details in prompts instead of "a guard monster" or "a rare pet". They come from the game's config modules (`Shared.Config.Biomes`, `Pets`, `EggVariants`), `DEV_NOTES.md`, the art-direction file `KEY_VISUALS.md`, and a Studio screenshot of the lobby, all as of 2026-10-13. They were **not** inspected live in Studio; re-check against the place before a final batch.

> [!warning] Guards and pets are still placeholders in the build
> DEV_NOTES "Open items" lists "low-poly meshes for guards, pets and the carried egg" as not done. The hen, croc and scorpion below are the **designed** look from `KEY_VISUALS.md`. Ship thumbnails that show them only once the in-game models match. Until then, a thumbnail showing a fluffy Mother Hen over a block-shaped guard is the expectation gap from [[Honesty vs clickbait]].

### Zones, guards and eggs

Zones unlock in order as Speed grows. The guard sleeps by its nest and wakes ("!!") when an egg is taken.

| Zone | Floor colour (RGB) | Zone accent | Guard | Guard look | Egg look |
|---|---|---|---|---|---|
| **Meadow** (start) | Grass green 63,165,0 | Sunny yellow | **Mother Hen** | Big fluffy white hen; angry, feathers puffed up when awake | Cream with yellow spots |
| **Riverbank** (300 Speed) | Teal green 30,160,70 | Sky cyan | **River Croc** | Big green crocodile, long and low, with a lily pad on its head | Blue with white wave stripes |
| **Canyon** (8,000 Speed) | Sandstone orange 214,146,52 | Ember orange | **Canyon Scorpion** | Chunky rust-red scorpion | Terracotta with cracked-clay lines |

Planned future zones (not in the game; don't show them yet): Snow (Yeti), Volcano (Magma Golem), Cosmic (Star Whale).

**Egg variants** (any zone; rarer ones start bigger, grow bigger and carry more luck):

| Variant | Chance | Look |
|---|---|---|
| Plain | 44% | Zone colours |
| Speckled | 22% | Tinted speckles |
| Spotted | 14% | Pebble texture |
| Marbled | 10% | Marble texture |
| Shiny | 6% | Glassy, glowing |
| **Golden** | 3% | Gold foil, glowing |
| **Rainbow** | 1% | Colours cycling through the rainbow, glowing |

A Rainbow or Golden egg is honest as the "hero" egg: it exists, it is rare, and it is the one players chase.

### Pets (what hatches)

| Zone | Common | Uncommon | Rare | Epic | Top |
|---|---|---|---|---|---|
| Meadow | Chick, Bunny | Lamb | Fox | **Owl** (brown) | **Golden Goose** (Legendary) |
| Riverbank | Duckling, Frog | Otter | Beaver | Heron | **Swan Queen** (Legendary) |
| Canyon | Lizard, Roadrunner | Armadillo | Coyote | Vulture | **Thunderbird** (Mythic) |

- **Every new player's first egg hatches an Epic Owl**, so the Owl is the most honest "first payoff" pet to show.
- Dream-pet looks from `KEY_VISUALS.md`:
  - **Golden Goose:** plump, polished gold, smug half-closed eyes, one curled crown feather.
  - **Swan Queen:** white swan, a crown of floating water droplets, a long tail like a ball gown.
  - **Thunderbird:** cyan-and-gold eagle with zigzag feathers and lightning crackling at the wing tips.
- Secret pets (Eggbert, Lilypad Leviathan, Sunstone Sphinx) are **design ideas only**; leave them out until they ship.

### Things players see that make a scene read as this game

- **The egg billboard** above a rolled egg: its weight in kg on top, then a **green four-leaf clover** and a luck number such as **x1.50**. It ticks up as the egg grows. This is the cleanest piece of real UI to add to a thumbnail by hand.
- **The gold SAFE ZONE line** on the ground: the guards stop there. Shown with the words "SAFE ZONE" on the Meadow floor.
- **The lobby:** a bright green studded floor, a giant golden egg statue on a cream pedestal, terracotta walls with lime trims, plots around a central plaza, the "Biggest egg today" board and the "NEW EGGS IN 5:00" egg reset.
- **Plots:** placed eggs incubate with a countdown, then hatch into pets that earn cash per second.
- **Night:** eggs and the world go blue-lit at night.
- **HUD numbers:** speed in cyan, money in bright green, both in chunky italic text with a dark outline (Rubik Bold Italic). Don't put HUD panels in thumbnails, but the same text style works for added numbers.

## Fantasy block

Paste this into every prompt, before the style block:

```
Core idea the image must show at a glance: the player has just stolen an egg from a nest guarded
by a giant animal and is ROLLING it home along the ground like a giant snowball. The egg grows
bigger with every step and is already much bigger than the player. The angry guardian animal is
chasing them. The player is winning: fast, excited, just out of reach.
```

Then name the zone's real guard and egg in the concept itself, for example "a huge fluffy white mother hen" with "a cream egg with yellow spots".

## Thumbnail prompts for Roll and Steal an Egg!

The concepts below are distinct, so they test as different thumbnails, but they all sell the same loop. Attach your Studio screenshots (guard, rolled egg, nest, Owl pet) as references wherever the tool allows it.

**1. The getaway roll** (hero shot; closest to the proven Steal an Egg chase)
```
Low camera, the Roblox avatar sprinting toward the camera while pushing a gigantic glowing
rainbow egg that rolls on the ground in front of them; the egg is twice the avatar's height and
leaves a trail of dust and sparkles behind it. Right behind them a huge fluffy white mother hen,
feathers puffed up and eyes furious, lunges with wings spread but is just out of reach. Far in
the background, a straw nest with small cream eggs with yellow spots. Bright green studded
grass, blocky trees, plain blue sky. The egg is the biggest, brightest thing in the frame.
[fantasy block] [style block] [avatar block]
```
Optional text: the egg's clover luck tag, for example a green four-leaf clover and "x4.80".

**2. The snowball trail** (growth told without words)
```
High three-quarter view of one curving path through a bright green meadow, from a straw nest in
the far background to the foreground. Along the path the SAME cream egg with yellow spots
appears three times, growing: tiny at the nest, medium halfway, enormous in the foreground
where the avatar is pushing it and grinning. A fluffy white mother hen stands at the nest,
flapping angrily. Path clearly visible, simple blocky trees.
[fantasy block] [style block] [avatar block]
```
Optional text: "x1.00" by the tiny egg and "x4.80" by the giant one, each with the clover icon.

**3. Small egg vs giant egg** (before/after; sells "bigger = rarer")
```
Split-screen, same avatar on both sides. Left (dull grey background): the avatar rolls a tiny
plain cream egg, and a plain little yellow chick pops out; the avatar shrugs. Right (bright
golden background): the avatar rolls an enormous glowing golden egg bigger than a house, and a
plump golden goose made of polished gold, with smug half-closed eyes and a curled crown
feather, bursts out of it with light rays; the avatar cheers. Thick black divider.
[fantasy block] [style block] [avatar block]
```
Optional text, using the real clover icon: "x1 LUCK" vs "x1,000 LUCK", or "TINY" vs "GIANT".

**4. Across the safe line** (the close call; the stealing thrill)
```
Side-on action shot at a teal-green riverbank: the avatar and their giant blue egg with white
wave stripes are mid-leap across a glowing gold line painted on the ground. A big green
crocodile with a lily pad on its head skids to a stop exactly at the line, jaws open inches
from the egg, furious. The avatar looks back laughing. Bright clean ground on the safe side,
reeds and water on the crocodile's side. [fantasy block] [style block] [avatar block]
```
Optional text: "SAFE ZONE" on the line, as it reads in the game.

**5. Bigger than everything** (oversell the power fantasy)
```
Wide shot in a sandstone-orange canyon: the avatar pushes a terracotta egg with cracked-clay
lines so enormous it towers over mesas and cacti, rolling it away from a nest, while a chunky
rust-red scorpion, now tiny next to the egg, scuttles away from it in panic. Dust, motion
lines, sparkles. The avatar is small but clearly in control and smiling. Blue sky.
[fantasy block] [style block] [avatar block]
```

**6. Day 1 vs Day 100** (progression: weak but winning, then overpowered)
```
Split-screen. Left, "Day 1": the avatar rolls a pebble-sized cream egg away from a small sleepy
white hen in a green meadow, looking proud. Right, "Day 100": the same avatar, now with a
glowing trail, rolls a gigantic shining rainbow egg out of a dramatic orange canyon while a
giant rust-red scorpion falls behind in the dust. A cyan-and-gold thunderbird with lightning
crackling at its wing tips flies beside the avatar. Same avatar, same art style.
[fantasy block] [style block] [avatar block]
```
Add the text "DAY 1" and "DAY 100" yourself, in a thick, simple font.

**7. Over-the-shoulder steal** (puts the player in the action)
```
Over-the-shoulder view from just behind the avatar's head: both blocky hands pushing a huge
glowing golden egg forward down a bright green meadow path toward a glowing gold line in the
distance. In the corner of the frame, a fluffy white hen's wing and furious eye reach in from
behind, almost touching the avatar. Sense of speed. [fantasy block] [style block]
```

**8. The hatch payoff, with the steal still visible**
```
Foreground: a giant egg cracking open on a farm plot with golden light rays, and a round brown
cartoon owl bursting out, wings spread; the avatar cheers next to it with both arms up.
Background, small but clear: the white mother hen at the edge of the meadow, sulking, and the
long trail in the grass where the egg was rolled. [fantasy block] [style block] [avatar block]
```
The Owl is honest here: every player's first egg hatches an Epic Owl. Attach its in-game look.

**9. Shocked face** (meme-style; cheap to test)
```
Extreme close-up of the avatar's shocked, delighted face, mouth wide open, as the gigantic
rainbow egg they are rolling looms over them, filling the top half of the frame. A tiny angry
white hen is visible in the gap between them, chasing. Plain bright red background.
[fantasy block] [style block] [avatar block]
```

**10. Girl set** (run it next to a boy set)
```
Same composition as concept 1, but the avatar is a girl Roblox avatar with pink hair and a pink
outfit, rolling a giant sparkly rainbow egg away from a silly-looking, flustered white mother
hen. [fantasy block] [style block]
```

**11. The wake-up** (tension before the chase)
```
Extreme low angle in an orange sandstone canyon. A giant chunky rust-red scorpion sleeps curled
around a straw nest of terracotta eggs, with "Zzz" curls above it and one eye just cracking
open. A tiny avatar tiptoes toward the eggs in the foreground, already reaching for the biggest
one. Mesas and cacti behind, warm sunset rim light, tension and comedy.
[fantasy block] [style block] [avatar block]
```
Optional text: a red "!!" over the scorpion, as the guards show when they wake.

**12. Studio scene, enhanced** (most honest; uses your real build)
```
[Attach a Studio screenshot of your avatar rolling a big egg away from the Mother Hen in the
Meadow, with the clover luck tag visible above the egg.]
Turn this exact scene into a polished cartoon thumbnail. Keep the egg, guard, avatar and colors.
Make the egg 2x bigger, move the avatar and egg closer to the camera, add motion lines and a
dust trail behind the rolling egg, give the avatar an excited face, simplify the background into
flat bright shapes, and add thick dark outlines around the avatar, egg and guard.
[fantasy block] [style block]
```

**13. The plaza** (social; the "join friends" angle, for the detail page)
```
High three-quarter view of a cheerful lobby on a bright green studded floor: a giant golden egg
statue on a cream pedestal in the middle, fenced farm plots around it with eggs incubating and
cartoon pets standing beside them, terracotta walls with lime-green trims. Several Roblox
avatars roll big colourful eggs in from an arched gate. Beyond the gate, green, teal and orange
zones in the distance. [style block] [avatar block]
```
This one doesn't show the chase, so use it on the detail page or as a secondary test, not as the main home thumbnail.

### The 100-pixel test, for every candidate

Shrink each image to about 100 px wide and ask someone who hasn't seen the game, ideally a kid in the target age range, "What do you do in this game?"

**Keep it** if they say something like "roll a giant egg away from a monster" or "steal an egg and it gets bigger". **Rework it** if they say "it's about pets" or "a monster game". Then:
- make the egg bigger and closer;
- add the trail or dust behind the egg;
- make the guard more obviously chasing.

### Text on the image

Text is optional ([[Composition, contrast and text]]). If you add it:
- Use at most a few words: STEAL · ROLL · GIANT, "SAFE ZONE", "x1 vs x1,000 LUCK".
- The clover plus "xN.NN" is real in-game UI, so it is honest and recognisable to players who have played.
- Add it yourself, in a thick, outlined font.
- Never use the game title or a logo.
- Test a no-text version of every winner.

### Reskin prompt

Gives an honest version of a proven layout:

```
[Attach: 1) a top-performing genre thumbnail for LAYOUT ONLY, 2) screenshots of my avatar, guard,
rolled egg and pets.] Create a new thumbnail that borrows only the composition and camera angle
of image 1, but shows MY game's idea: the player rolling a giant growing egg away from a guard.
Use my characters and objects from the other images. Do not copy any character, logo, text or
artwork from image 1. [fantasy block] [style block]
```

### Variation prompts for a winner

Use these after a thumbnail wins, one change at a time:
- `Same image, but make the rolling egg 50% bigger and closer to the camera.`
- `Same image, but add a dust and sparkle trail behind the rolling egg.`
- `Same image, but the egg is diamond-blue instead of gold, so it stands out from everything else.`
- `Same image, but the guard is closer, almost touching the avatar.`
- `Same image, but in the Riverbank: teal ground, a blue wave-striped egg, and a green crocodile with a lily pad on its head.`
- `Same image, but swap the background to a flat bright color and remove all clutter.`
- `Same image, mirrored, with the avatar rolling left to right.`
- `Same image, no text version` (to test against the version with text).

### Clean-up prompts

- `Fix the hands so each has four blocky fingers like a Roblox character.`
- `Make the egg clearly touch the ground and roll; it must not float or be held.`
- `Remove the extra limbs and any muddy texture; keep everything else identical.`
- `Make it less realistic and more like a clean cartoon render; flatten the grass into a simple color.`
- `Upscale to 1920x1080, sharpen edges, keep colors identical.`

## Game icon prompts (512×512)

Most players say icons matter less than thumbnails, and Roblox shows no icon CTR. The common move is to adapt your best thumbnail. Even in a square, show the **roll**, not just an egg.

**Icon A, rolling the giant egg:**
```
Square game icon. Side view of a Roblox avatar leaning forward, pushing a gigantic glowing
rainbow egg that fills two-thirds of the frame; dust puffs under the egg show it is rolling.
Solid bright blue background, thick black outlines. Readable at 150x150 pixels. No text.
[avatar block]
```

**Icon B, the close call:**
```
Square game icon. The avatar's excited face in the foreground and their giant rolling golden
egg beside them, with a furious fluffy white hen's wing reaching in from the corner, almost
touching the egg.
Bold colors, strong contrast, solid orange background. No text. [avatar block]
```

**Icon C, the snowball egg:**
```
Square game icon. One huge cream egg with yellow spots, rolling, with motion lines and a short
trail of smaller "ghost" eggs behind it showing that it grew, a tiny avatar pushing it, bright
meadow-green studded background. No text. [avatar block]
```

Optional number badge: add short text like "1M" in Photopea, not with the AI.

## Item and UI icons

Use these for shop, eggs, currency and Robux products. Generate them as a matched set:

```
2D game UI icon, front view, of [a glowing golden egg], chunky cartoon style, bold
black outline, simple 3-tone shading with a white highlight, centered, transparent background
(or plain flat magenta background for easy removal), consistent with a set of Roblox simulator
shop icons. No text.
```

Swap the bracketed item for each icon:
- `a stack of green cash`
- `a running shoe with speed lines`
- `a hamster wheel`
- `a cracked egg with a pet peeking out`
- `a lucky four-leaf clover`
- `an hourglass for instant hatch`
- `a treasure chest`

For an existing set's look, attach two or three of your current icons and add: `match the line weight, shading and colors of the attached icons.`

**Free route without AI**, for items that exist in-game:
1. Screenshot the model in good light.
2. Cut it out with remove.bg.
3. Add stroke and colour correction in Photopea.

## Testing checklist

- [ ] At least 10 truly different concepts, not 10 colour swaps of one.
- [ ] Every thumbnail passes the 100-pixel test: a stranger can tell you to steal an egg and roll it home as it grows, with no title.
- [ ] Guards, eggs and pets are the real cast (Mother Hen, River Croc, Canyon Scorpion; zone eggs; real pets), and the in-game models match what the thumbnail shows.
- [ ] Every thumbnail shows the **real** loop (steal, roll and grow, escape, hatch) with real in-game eggs, guards and pets. Ad CTR that doesn't carry over to home PTR usually means clickbait.
- [ ] Readable at about 100 px; the subject is larger than any background object.
- [ ] Nothing important in the bottom 15%.
- [ ] No Robux logo, no game title or logo on the thumbnail, no copied IP.
- [ ] 1920×1080, under 3 MB.
- [ ] Judge each after about 10k+ impressions. Below about 3% is the common cut line.
- [ ] Home page: keep 2–5 active so personalization works. Detail page: upload separately.

## Related notes

- [[Show the core fantasy and loop]]: the rule these prompts are built on.
- [[Thumbnails and Icons]]: CTR targets, testing volume, kill/keep rules, winners and flops.
- [[Examples of winners and flops]]
- [[Honesty vs clickbait]]
- [[Designing for kids and age gates]]
- [[Composition, contrast and text]]
- [[AI workflows, tools and artists]]
- [[Ad CTR vs home PTR and page setup]]
- [[Ads and Ad Credits]]

## Sources

- Roblox creator docs (official): Thumbnails, Icons. In the `Roblox/creator-docs` repo: `content/en-us/production/publishing/thumbnails.md` and `experience-icons.md` (repo state 2026-10-13). AUTHORITATIVE.
- *Hud's Guide To Roblox Success*, chapter 07, especially "Show the core fantasy and loop" (community observations, May–Oct 2026). USEFUL, community observation.
- This repo's `DEV_NOTES.md` and `handoffs/`, for what the game actually does (rolling growth, luck, guards, safe line, hatching). AUTHORITATIVE for this game.
- The game's config modules `Shared.Config.Biomes`, `Pets` and `EggVariants`, the art-direction file `KEY_VISUALS.md`, and a Studio lobby screenshot, as captured in this repo's session transcripts (2026-10-11 to 2026-10-13). Not inspected live in Studio. PROJECT-VERIFIED, may be out of date.
- Free-tier reports (third-party, figures conflict). UNVERIFIED:
  - [Rangy: Best Free AI Image Generators 2026](https://rangy.ai/blog/best-free-ai-image-generator/)
  - [Wireflow: 10 Tools Compared](https://www.wireflow.ai/blog/best-free-ai-image-generators-compared-in-2026)
  - [Zenken: Gemini Image Generation daily limits](https://ai.zenken.co.jp/en/post/gemini-image-guide/)
  - [LetsDataScience: Nano Banana Pro free cut to two a day](https://letsdatascience.com/news/google-cuts-free-nano-banana-pro-image-generation-to-two-ima-175236df)
  - [CometAPI: ChatGPT free image limit 2026](https://www.cometapi.com/how-many-images-can-you-create-with-chatgpt-free-in-2026/)
  - [Tom's Guide: ChatGPT image generator free for all](https://www.tomsguide.com/ai/chatgpts-updated-image-generator-is-now-free-for-all-but-theres-a-catch)
  - [Bing blog: Image Creator adds GPT-4o](https://blogs.bing.com/search/August-2025/Bing-Image-Creator-gets-GPT-4o)
  - [Templated: Bing Image Creator guide](https://www.templated.io/blog/bing-image-creator-guide)
  - [Recraft llms.txt](https://www.recraft.ai/llms.txt)
  - [Krea: Ideogram pricing and alternatives](https://www.krea.ai/blog/de/what-is-ideogram-ai-features-pricing-and-alternatives-in-2026)
  - [kocpc: Google Flow credits](https://en.kocpc.com.tw/?p=3874)

## Last Verified

2026-10-14. Roblox specs were checked against the official docs; free-tier figures are third-party reports.
