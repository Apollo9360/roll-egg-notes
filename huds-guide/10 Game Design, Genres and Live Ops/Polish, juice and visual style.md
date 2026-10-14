---
tags: [huds-guide, game-design]
---

# Polish, juice and visual style

Up: [[Game Design, Genres and Live Ops]] · Previous: [[Designing for kids, audiences and naming]] · Next: [[Multiplayer and PvP]]

- Good design is quality; art direction is only part of it.
- Below about 5k CCU, quality matters less; toolbox assets are fine until traction, then fund a remake (disputed).
- Match visuals to theme: vibrant lucky-block games, not dark high-tech; no neon UI in horror.
- Do not mix low-poly and high-poly, or put brainrot studs on an original simulator idea.
- Stud look: Baseplate texture plus studs, mixing stud and smooth materials. Studs do not decide virality.
- AI 3D generation suits realistic models but not stud style.
- For an intimidating look, use less cartoony, less saturated, more detailed models. Plushie-style "mascot horror" makes horror accessible.

### Lighting and map

- Cartoon lighting (Grow a Garden style): white ambient, ColorCorrection with a slight saturation bump, vibrant textures and skybox.
- Simulator lighting: more yellow ambient. For +1 games, tweak saturation via ColorCorrection.
- Use multiple soft light sources where players move. Denser fog suits a mystical look.
- Lighting sets mood and contrast; the map should guide the player. Generic AI map prompts give low quality.
- Map polish: falling leaves, grouped flowers, varied trees, moving clouds, ambient birds and wind.

### UI

- Consistency beats art style. Icons should match, and feedback should feel rewarding.
- Match styles and fonts. Make text strokes as thick as icon strokes. Round corners. Do not overuse stud texture.
- Full AI-made UIs look AI-generated with poor hierarchy. Buy a UI pack (about $20–30) or build from frames, using AI only for icons.
- Make loading screens eye-catching or skip them; put the skip button right below the bar.
- Too many preloaded assets freeze the game on join; high receive bandwidth (around 120 kbps) lags.
- Prompt group joins with a lobby sign or proximity prompt, not a pop-up. `GroupService:PromptJoinAsync` supports this.

### Genre-specific polish

- Horror:
    - Scope to about a week. Beyond The Shelf took 8 days and got 1M visits through small details and a strong story.
    - Prototype one scenario incrementally; a phone buzz before a kitchen sound made a scare far stronger.
    - Chase sequences must feel intense.
- Animal Hospital-style concept: solo or co-op, NPC story, secretly horror-ish, no jumpscares. It grew from about 500 to 500k CCU via TikTok, pacing and humor.
- Card, table and word games can come from TikTok challenges but bore players after 3–4 minutes without a return mechanic.
- Mob triangle budgets by mob count:

| Mob count | Triangles per mob |
|---|---|
| Hordes | 500–2,000 |
| Many | 2,000–5,000 |
| Normal or small counts | 5,000–10,000 |
| Above 10,000 | Highly detailed only |

- A pet ranch with about 8 pets in each of 6 player lobbies should use 2,000–5,000 triangles per pet.

---
Related concepts: [[AI Tools]] · [[Genres and Saturation]] · [[Icons and Titles]] · [[CCU (Concurrent Users)]] · [[Moderation and Bans]]
