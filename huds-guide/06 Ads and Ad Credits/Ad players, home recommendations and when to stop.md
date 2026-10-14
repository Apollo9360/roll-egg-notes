---
tags: [huds-guide, ads]
---

# Ad players, home recommendations and when to stop

Up: [[Ads and Ad Credits]] · Previous: [[Testing, beta and multiplayer games]] · Next: [[Other paid channels and history]]

- Ads exist to give the recommendation system data; success comes from home recommendations, not ads.
- Ad players have worse retention, monetization and playtime than home-recommendation players. They rarely buy.
- On day 1, 2–7 minutes of playtime is common, and most ad players leave within 5 minutes.
- Aim for at least about 8 minutes of ad playtime, or about 30% of your genre's benchmark.
- Don't worry about ad-traffic retention unless it is below about the 25th–30th percentile.
- Plays ads can bring AFK default-avatar or bot accounts that leave within seconds. Claims that rivals bot competitors' ads are low confidence.
- You earn spend back from home-recommendation players. D7 stays low until they arrive; offline rewards and come-back incentives help.

### Expected CCU on ads

| Daily budget | Typical ad CCU | Confidence |
|---|---|---|
| 5 credits | 1–5, or a 50 spike that fades | Medium |
| 8 credits | 6–12 | Low |
| 16 credits | 20–50 average; 30–50 on day 1, up to 100–150 by day 3 | Medium |
| 16 credits | Spikes of 200–350 lasting about 20 minutes | Medium |

- Ad CCU depends on spend, CTR and playtime: 30-minute sessions give about 3x the CCU of 10-minute ones.
- Ad CCU is for gathering stats, not holding CCU. Ignore it unless it is extremely low, which signals a major bug.

### When to stop

- Stop when home-recommendation impressions or plays exceed sponsored ones (owner-recommended). This has been the consistent main rule.
- Other cut points: half of impressions from home recommendations, 30–50 CCU from them, or home recommendations alone holding 100–150 CCU.
- Daily home-recommendation impression targets vary widely, from 20k to 100k.
- A game getting 2–3k+ home-recommendation impressions per hour no longer needs ads.
- Don't stop at the HEP threshold unless you get no home recommendations at all.
- Taper if affordable (18 to 15/day, or 14 to 12 to 10/day).
- A CCU drop when ads end is normal. Without home recommendations, CCU falls to about 0–5.
- Several developers saw CCU and stats improve after turning ads off, as ad players stopped diluting averages.

### Ads after reaching home recommendations

- Disputed. Ad players can dilute good home-recommendation stats; one game's D1 fell from 15% to 5% with ads on.
- Turning ads back on has made home recommendations vanish for days, and lowered spend by organic players.
- The owner keeps ads running after the algorithm picks up a game to test new thumbnails, and says ads can theoretically run forever.
- Big games run ads continuously for reactivation or profit; very large shooters still advertise at 200k CCU.
- A mid-size example: about 30k Robux of ads every weekend sustained a game at 1–2k CCU.
- Wait until home recommendations dry up before restarting ads on an established game.

### When to give up

- After 5–8 days of poor stats, ads are unlikely to save the game.
- No home recommendations after about 2 weeks at 16/day means the game isn't making it (owner agrees).
- No HEP after over a week, or stats under about the 20th percentile, means the game has a problem.
- Good CTR but high CPP and poor revenue after 2+ weeks: pause and fix monetization.
- Fix pain points, get QA (quality assurance) testing and rerun. If stats still don't improve, move on.
- Games that blow up within a week usually come from established studios, very good games or viral marketing, not $100/day ads.

---
Related concepts: [[Home Recommendations]] · [[CCU (Concurrent Users)]] · [[Playtime]] · [[Ad Credits]] · [[CTR (Click-Through Rate)]] · [[HEP (Highly Engaged Players)]] · [[Test Bursts and Spikes]] · [[D1 Retention]]
