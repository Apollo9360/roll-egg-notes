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
Style: Roblox game thumbnail, bright saturated toy-brick world, chunky studded plastic surfaces,
voxel-style pets and eggs that glow by rarity, cartoon 3D render with soft lighting, bold clean
shapes, high contrast between subject and a simple background, thick dark outlines around main
subjects, 16:9 landscape. Keep the bottom 15% of the image free of important details. No text,
no logos, no watermark, no UI, no Robux symbol.
```

### Avatar block

Use this for kid-relatable avatars:

```
The player is a classic Roblox avatar: blocky body, "bacon hair" brown hairstyle, simple smiling
cartoon face, plain t-shirt; proportions exactly like a standard Roblox character, not a realistic
human. Clean hands with no extra fingers.
```

Generate **without text**, then add text yourself. Ask for "no text"; AI lettering is unreliable even when it looks fine at full size.

## Thumbnail prompts for Roll and Steal an Egg!

Each concept is a different idea, so they test as distinct thumbnails. Attach your Studio screenshots as references wherever you can.

**1. The steal chase.** This is the proven Steal an Egg format.
```
A Roblox avatar sprinting toward the camera hugging a giant glowing golden egg, terrified-excited
face, a huge angry guard monster chasing right behind with arms reaching out, motion lines, dust
kicking up. Plain bright green studded ground and a simple blue sky. The avatar fills the left
two-thirds of the frame; the guard looms behind on the right. [style block] [avatar block]
```

**2. Egg size before/after.** Exaggerate the size difference.
```
Split-screen before/after. Left side: a Roblox avatar holding a tiny plain white egg, bored face,
dull grey background. Right side: the SAME avatar straining to carry an enormous rainbow egg twice
their height, shocked happy face, glowing sparkles, bright yellow background. A thick black
vertical divider. [style block] [avatar block]
```
Add the text yourself: "SMALL" vs "HUGE", or "1 vs 1,000,000".

**3. Day 1 vs Day 100.**
```
Split-screen comparison of a player's plot. Left: an empty dirt plot with one cracked small egg.
Right: a packed plot with six giant glowing eggs on incubators, a spinning prize wheel and a crowd
of rare voxel pets with golden auras. Same Roblox avatar in both halves, sad on the left, cheering
on the right. [style block] [avatar block]
```

**4. The prize wheel.**
```
Close-up of a Roblox avatar slamming a giant colorful prize wheel, the pointer landing on a glowing
"LEGENDARY" slice (render the slice as a golden star icon, no letters), coins and sparkles exploding
outward, the avatar's mouth wide open. Simple purple radial background. [style block] [avatar block]
```

**5. The hatch reveal.**
```
A giant egg cracking open with blinding golden light beams, a cute voxel owl pet bursting out with
wings spread and big sparkly eyes, the Roblox avatar recoiling in amazement in the foreground,
confetti-like sparkles. Dark blue simple background so the light pops. [style block] [avatar block]
```

**6. First-person grab.**
```
First-person view: two blocky Roblox hands reaching to grab a huge glowing purple egg from a nest,
a sleeping guard monster just behind the nest with one eye opening. Tension, close crop, the egg
dominates the center. [style block]
```

**7. "YOU" versus the guard.**
```
Low-angle hero shot: a small Roblox avatar standing defiantly holding an egg above their head,
facing a gigantic guard monster that fills the background, the guard's shadow over the avatar.
Leave clear empty space above the avatar's head for a label. [style block] [avatar block]
```
Add a red "YOU" arrow pointing at the avatar.

**8. Rarity ladder.** Shows the collection fantasy.
```
A row of five eggs growing in size from left to right: grey, green, blue, purple, golden-rainbow,
each glowing brighter with sparkles, the last one huge with light rays. A Roblox avatar at the end
pointing at the biggest one with an excited face. Clean light-blue background. [style block]
[avatar block]
```

**9. Stealing from a friend.** The social fantasy; stealing tests better than defending.
```
A sneaky Roblox avatar tiptoeing away from another player's plot carrying a giant shiny egg, the
other player in the background turning around shocked. Cartoon "sneaky" pose, finger to lips.
Bright simple plots with low fences. [style block] [avatar block]
```

**10. Meme reaction.** Lower effort, sometimes wins.
```
Extreme close-up of a Roblox avatar's face with a shocked meme expression, eyes popping, reflected
in their eyes a giant glowing rainbow egg. Plain red background. [style block] [avatar block]
```

**11. Girl set.** Run alongside a boy set for mixed audiences.
```
Same scene as concept 1 but the avatar is a girl Roblox avatar with pink hair and a pink outfit,
carrying a giant pastel-pink sparkly egg with a cute bunny-ear pattern, a silly-looking guard
chasing. [style block]
```

**12. Studio-scene enhance.** Uses your real build.
```
[Attach a Studio screenshot of your nest zone with your avatar and a guard.] Turn this exact scene
into a polished cartoon game thumbnail. Keep the layout, the egg, the guard and the colors, but
make the avatar 2x bigger and closer to the camera, add an excited expression, motion and glow on
the egg, simplify the background into flat bright shapes, and add a thick dark outline around the
avatar, egg and guard. [style block]
```

### Reskin prompt

Gives an honest version of a proven layout:

```
[Attach: 1) a top-performing genre thumbnail for LAYOUT ONLY, 2) screenshots of my avatar, guard,
eggs and pets.] Create a new thumbnail that borrows only the composition and camera angle of
image 1, but uses my characters and objects from the other images. Do not copy any character,
logo, text or artwork from image 1. [style block]
```

### Variation prompts for a winner

Use these after a thumbnail wins, one change at a time:
- `Same image, but make the main character 30% bigger and closer to the camera.`
- `Same image, but swap the background to a flat bright yellow and remove all clutter.`
- `Same image, but the egg is diamond instead of gold so it stands out from everything else.`
- `Same image, but change the guard to a different, scarier monster.`
- `Same image, mirrored, with the character running left-to-right.`
- `Same image, no text version` (to test against a version with text).

### Clean-up prompts

- `Fix the hands so each has four blocky fingers like a Roblox character.`
- `Remove the extra limbs and any muddy texture; keep everything else identical.`
- `Make it less realistic and more like a clean cartoon render; flatten the grass into a simple color.`
- `Upscale to 1920x1080, sharpen edges, keep colors identical.`

## Game icon prompts (512×512)

Most players say icons matter less than thumbnails, and Roblox shows no icon CTR. The common move is to adapt your best thumbnail. Fill the square with **one** subject.

**Icon A, face plus egg:**
```
Square game icon. Close-up of a Roblox avatar's excited face hugging a giant glowing golden egg
that fills most of the frame, bright sparkles, solid bright blue background, thick black outline
around the character and egg. Readable at 150x150 pixels. No text.
```

**Icon B, the guard:**
```
Square game icon. A giant cartoon guard monster's face peeking from the edge, glaring at a shiny
egg in the foreground. Bold colors, strong contrast, solid orange background. No text.
```

**Icon C, the cracking egg:**
```
Square game icon. One huge rainbow egg cracking with golden light rays bursting out, centered,
on a deep purple background, chunky studded texture on the shell. No text.
```

Optional number badge: add short text like "1M" in Photopea, not with the AI.

## Item and UI icons

Use these for shop, eggs, currency and Robux products. Generate them as a matched set:

```
2D game UI icon, front view, of [a golden egg with a crown pattern], chunky cartoon style, bold
black outline, simple 3-tone shading with a white highlight, centered, transparent background
(or plain flat magenta background for easy removal), consistent with a set of Roblox simulator
shop icons. No text.
```

Swap the bracketed item for each icon:
- `a stack of green cash`
- `a running shoe with speed lines`
- `a spinning prize wheel`
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
- [ ] Every thumbnail shows the **real** core loop: steal, run, place, roll, hatch. Ad CTR that doesn't carry over to home PTR usually means clickbait.
- [ ] Readable at about 100 px; the subject is larger than any background object.
- [ ] Nothing important in the bottom 15%.
- [ ] No Robux logo, no game title or logo on the thumbnail, no copied IP.
- [ ] 1920×1080, under 3 MB.
- [ ] Judge each after about 10k+ impressions. Below about 3% is the common cut line.
- [ ] Home page: keep 2–5 active so personalization works. Detail page: upload separately.

## Related notes

- [[Thumbnails and Icons]]: CTR targets, testing volume, kill/keep rules, winners and flops.
- [[Composition, contrast and text]]
- [[AI workflows, tools and artists]]
- [[Ad CTR vs home PTR and page setup]]
- [[Ads and Ad Credits]]

## Sources

- Roblox creator docs (official): Thumbnails, Icons. In the `Roblox/creator-docs` repo: `content/en-us/production/publishing/thumbnails.md` and `experience-icons.md` (repo state 2026-10-13). AUTHORITATIVE.
- *Hud's Guide To Roblox Success*, chapter 07 (community observations, May–Oct 2026). USEFUL, community observation.
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
