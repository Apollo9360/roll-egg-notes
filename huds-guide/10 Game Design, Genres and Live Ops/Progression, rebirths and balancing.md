---
tags: [huds-guide, game-design]
---

# Progression, rebirths and balancing

Up: [[Game Design, Genres and Live Ops]] · Previous: [[Core loop design]] · Next: [[Genre trends, May–October 2026]]

### Incremental basics

- An incremental builds on gradual resource accumulation with compound growth: farm, rebirth, restart stronger, repeat.
- Rules: understood in 10 seconds without a tutorial, no constant hand or brain effort, automated progression, rewarding hours-long progression.
- Incrementals are quick to make but hard to balance. Solo developers ship one in about 1–2 weeks; teams with reusable systems in about 7 days.
- About 2 hours of content is usually enough at launch. Some tune for 3–5 hours, slowing at about 50% and 70%.
- A full incremental can take about 10 days of playtime; one AI balancer projected 2 days.
- Sell Lemons-style games use three reset layers: prestige, void, ascension.
- Give players an end-goal stat or item to show off; remember the social flex element.
- In +1 and grow games, size and speed are intuitive rewards.
- Compare +1 speed stage times against competitors. Stages usually lengthen as players progress.
- Merging two identical items feels faster and more fun than merging three.
- Plan the economy for future updates. Grow a Garden fought inflation with social late-game sinks, a currency-conversion stand and trading.

### Rebirth design

- Modern games keep collected items (such as lucky blocks) on rebirth.
- The opposite also works: one steal-and-build game reset pets and blocks for a permanent luck multiplier, new gear and more trap slots.
    - It switched because kept cash-earning pets let kids reach end-game in 3–24 hours.
    - Players preferred prestige rewards over luck multipliers, and some handed rare pets to alternate accounts first.
- Fix the rebirth count before launch; adding tiers later effectively resets everyone (low confidence).
- Scale rebirth cost 2x–4x per rebirth, starting low (about 1k) for the tutorial.
- A money-multiplier-only rebirth feels weak. A rebirth shop spending rebirth points on chosen upgrades works better.
- Alternatives: timed runs that keep units and currency and grant a special currency; or reset the tool but keep wins and speed.

### Balancing method

- Players like feeling rich early: start fast, then slow down. AI scaling for +1 speed games tends to be too grindy early.
- Use separate exponential curves, with cost growing faster than income (such as +20% versus +10% per level).
- Failure example: a car-steal game went from 1k to 215M per second in 1–2 minutes against a 25M rebirth cost.
- Admin tools can break an economy; one panel bug let players reach end-game pets in 4 hours.
- Spreadsheet method:
    1. In Google Sheets, track cash per second, upgrade cost, multiplier gained, time to afford and payback time.
    2. Use cost = baseCost × growthRate^level and tune the growth rate.
    3. Simulate a player from zero to endgame to time each stage.
- AI can do the first math pass and simulate progression time, but never balance alone.
- Play 1–2 hours from a fresh start, simulating breaks. Watch others play, then repeat.
- For very long progressions, script an AI player with a "dopamine" stat to estimate fun and time to goal.
- Full balancing typically takes a few days. Level designers can be hired for it.
- Decide on signal, not statistical significance, until daily active users (DAU) support a formal test (owner-recommended).
- If a change makes stats worse, revert it (owner-recommended).
- A/B test a feature by running one week with it and one without. Roblox reportedly added update testing on a share of players (low confidence).

---
Related concepts: [[Core Loop and Progression]] · [[AI Tools]] · [[Onboarding and Tutorials]] · [[Updates and Live Ops]] · [[Playtime]]
