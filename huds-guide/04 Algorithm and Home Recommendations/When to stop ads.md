---
tags: [huds-guide, algorithm]
---

# When to stop ads

Up: [[Algorithm and Home Recommendations]] · Previous: [[Getting re-tested and recovering]] · Next: [[Similar games and saturation]]

| Heuristic | Threshold | Confidence |
|---|---|---|
| Impression ratio | Home impressions equal or exceed ad impressions (owner-recommended); some wait for 2x | High |
| Player source mix | Home-rec plays overtake ad plays; some wait for about 50% of players from home recs | High |
| Home-rec CCU | Home recs alone sustain at least 10+ CCU | Medium |
| CCU level | Consistent 30+ CCU; stricter advice waits for 50–70 | Medium |
| Impression volume | At least 25k–100k home impressions; some wait for 100–150k | Medium |
| Home-rec plays | About 1,000+ plays a day from home recs | Low |
| Consistency | Steady algorithm impressions, not the first trickle | High |
| Minimum duration | 7 days nonstop at constant budget; 2 weeks is better | High |

- Most developers keep ads running until home recs clearly sustain the game. Otherwise the game usually dies right after ads end.
- A typical run: home recs by about day 5, then about 7 more days of ads until home impressions rise.
- If home and ad impressions are still close at 2 weeks, extend the ads.
- Big impressions sometimes arrive about 3 days after stopping ads.
- Home-rec impressions have doubled after ads were turned off. Ads may dilute stats while running.
- A minority keep ads running indefinitely, or as long as they stay profitable.
- Once home recs grow, ad-driven traffic matters little; home recs can exceed 99% of players.
- Use small ads around big updates, or to re-target lapsed players as home recs bring fewer returners.
- If the game already gets home recs without ads, ads add little. If needed, run about 9 credits/day until home impressions rise.
- Minigame or multiplayer games may still need ads after home recs dominate.
- To grow from 400–500 CCU to 1k+ with good stats, keep investing in ads.

---
Related concepts: [[Home Recommendations]] · [[CCU (Concurrent Users)]] · [[Ad Credits]] · [[Updates and Live Ops]]
