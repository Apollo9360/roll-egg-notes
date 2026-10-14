---
tags: [huds-guide, analytics]
---

# Estimates and projections

Up: [[Analytics and Metrics]] · Previous: [[Ads Manager reporting quirks]] · Next: [[Known bugs, external tools and performance stats]]

All figures below are estimates derived from reported numbers, not reported facts.

### CCU, DAU and revenue

The identity CCU ≈ DAU × average daily playtime (min) ÷ 1440 links the stats. A reported example: 500 CCU at 15 min needs about 48k DAU.

| Average CCU | DAU at 15 min playtime | DAU at 30 min playtime | Robux per day at 0.25 ARPDAU (15 min) | Robux per day at 0.7 ARPDAU (15 min) |
|---|---|---|---|---|
| 100 | About 9,600 | About 4,800 | About 2,400 | About 6,700 |
| 250 | About 24,000 | About 12,000 | About 6,000 | About 16,800 |
| 500 | About 48,000 | About 24,000 | About 12,000 | About 33,600 |
| 1,000 | About 96,000 | About 48,000 | About 24,000 | About 67,000 |

- Estimate: the 250 CCU row is interpolated between reported 100 and 500 CCU examples (basis: the same identity).
- Estimate: doubling playtime halves the DAU behind a given CCU, and so roughly halves revenue at equal ARPDAU (basis: identity above).
- Estimate: at 0.1 ARPDAU, 500 CCU earns only about 4,800 Robux per day (basis: 48k DAU × 0.1).
- Estimate: sessions per day ≈ 24 × CCU ÷ session hours. 100 CCU is about 7,200 sessions at 20 min, but 1,200 at 2 h.

### Sample sizes for meaningful differences

- Estimate: telling 3% CTR from 4% needs about 5k impressions per thumbnail, and 3% from 3.5% about 20k (basis: two-proportion test, 95% confidence, 80% power).
- Estimate: a 15% bounce rate is accurate to about ±7 points at 100 first plays, and ±2 at 1,000 (basis: binomial 95% interval).
- Estimate: a 10% D1 from 100 new players is ±6 points; from 1,000, about ±2 points (basis: binomial 95% interval).

### Ads and HEP

- Estimate: $100 of ads buys roughly 10k–50k plays at $0.002–0.01 CPP (basis: reported CPP range).
- Estimate: at about 50% clicks-to-plays, each 1,000 ad clicks yields about 500 plays (basis: owner target).
- Estimate: reaching 250 HEP needs roughly 550–1,250 engaged players (basis: about 40–45% conversion in one report; 20% assumed for young or free-to-play audiences).
- Estimate: at the announced 100 HEP threshold, the same assumptions give roughly 220–500 engaged players.
- Estimate: with zero new acquisition, a game with 30 min average playtime empties in about 30 min.

---
Related concepts: [[CCU (Concurrent Users)]] · [[Playtime]] · [[ARPDAU]] · [[HEP (Highly Engaged Players)]] · [[Cost Per Play (CPP)]] · [[CTR (Click-Through Rate)]] · [[Bounce Rate]] · [[D1 Retention]]
