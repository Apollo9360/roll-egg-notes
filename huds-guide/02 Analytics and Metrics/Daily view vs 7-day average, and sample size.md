---
tags: [huds-guide, analytics]
---

# Daily view vs 7-day average, and sample size

Up: [[Analytics and Metrics]] · Previous: [[Where to find things]] · Next: [[Data lag and update timing]]

- The Overview defaults to a 7-day rolling average ("Snapshot of 7-day moving averages").
- To switch, click "7 day avg" at the top right of a chart and choose Daily. Alternatively, Explore and set smoothing to None.
- The 7-day average includes pre-ad days. One game showed 2.5 min playtime that was really about 6.1 min for ad players.
- It also includes your own Studio and test sessions. A 64 min test or an alt account skews playtime and D1.
- Young games can show impossible retention. Two players joining 8 days ago with one returning shows 50% D7.
- Running ads for only 5 days of a 7-day window inflates the average.
- If the 7-day view looks great but daily looks bad, the latest traffic mix changed, often a new region from home recs.
- Daily benchmarks often look worse than the 7-day views people share.
- The 7-day average is meaningful only once a game is in the algorithm with stable stats.
- If a stat swings by more than 100%, there are too few players to measure it.

| Sample size | What it supports | Confidence |
|---|---|---|
| About 50 impressions | CTR confidence interval is very wide | High |
| 200–2k visits | Not judgeable | High |
| About 100 DAU | Benchmarks and stat colours start to mean something | Medium |
| About 5k ad impressions | Thumbnail CTR still inconclusive for small differences | Medium |
| 3–5k visits | Funnel step drop-offs fairly reliable | Low |
| About 10k visits | Stats stop being noise; funnels strong | High |
| 500–1,000 DAU | Playtime and retention trustworthy | Medium |
| 25k visits | Minimum some developers keep ads running before judging | Low |
| 100k–1M impressions | QPTR changes become meaningful | Medium |

- 50% D1 on 10 DAU is not comparable to 50% on 10k DAU. 22% D1 from 11 players means nothing.
- An 8% CTR on $0.10 of spend is meaningless. Ignore such numbers but keep monitoring.
- Some say not to obsess over analytics until new users per 7 days exceed about 100.
- Wait 3–4 days after release or ad start before judging. Good data usually takes about a week.
- Before then, watch funnels, tutorial completion and thumbnail CTR, which update faster. Fix drop points immediately.
- Beta-mode testing reportedly does not count toward stats (low confidence).

---
Related concepts: [[CTR (Click-Through Rate)]] · [[D1 Retention]] · [[Playtime]] · [[Funnels]] · [[Home Recommendations]] · [[Thumbnails]] · [[QPTR (Qualified Play-Through Rate)]] · [[D7 Retention]]
