---
tags: [huds-guide, analytics]
---

# How the home-recommendation algorithm reads your game

Up: [[Analytics and Metrics]] · Previous: [[Metric glossary]] · Next: [[Discovery metrics CTR, PTR, QPTR and bounce rate]]

"The algorithm" in community talk means home recommendations, and it reads only Home Recommendation Signals. Improve your worst signal first, not CCU or visits.

### How the signals evolved

| When | Change | Confidence |
|---|---|---|
| Early | Roblox ranked home recommendations largely on CTR | Medium |
| Next | Developers abused CTR with clickbait thumbnails that did not match the game | Medium |
| Next | QPTR was added to punish thumbnails that got clicks but lost players within about 5 min | High |
| May 19, 2026 | Analytics and ads update shipped; data from before it is probably not counted in the new views | Medium |
| June 15, 2026 | "Recommended For You" update: QPTR removed as a signal, split into PTR and first-play bounce rate | High |
| June 15, 2026 | Signal window grew from 7 to 28 days, measured separately for D1, D2–7 and D8–28 | High |
| Mid-2026 | New user first-session retention took QPTR's old spot under Retention | Medium |
| Early October 2026 | Benchmarks began comparing against "experiences with similar players" | Medium |
| October 12, 2026 | Creator Hub rework added a "Manage audience" page and moved the HEP chart | Medium |

- Official post: [Recommended for You algorithm improvements](https://devforum.roblox.com/t/recommended-for-you-algorithm-improvements-that-better-value-long-term-retention/4684575).
- The split lets you separate a packaging problem (thumbnail or game page) from an onboarding problem. QPTR could not.
- Only users who joined organically from the Home "Recommended For You" sort count toward the signals.
- Signals tracked in all three windows are playtime, play days, qualified play sessions, intentional co-play days, spend days and Robux spent.
- The relative weight of each signal is shown under Creator Analytics > Acquisition > Home Recommendations.
- Roblox reportedly uses machine learning to predict D7 and D28 before that data exists.

### Signal priority

| Rank | Signal | Confidence |
|---|---|---|
| 1 | PTR | High |
| 2 | First-play bounce rate | High |
| 3 | Play days per user | High |
| 4 | Playtime per user | High |
| Lower | Spend signals (spend days, Robux spent per user) | Medium |

- This order is the only ranking Roblox has published. Dashboard metrics such as D1, D7 and PCR are diagnostics, not ranked inputs.
- The owner calls PTR "the first step to high CCU" and says play days matter more than D1 (owner-recommended).
- Unofficial community rankings vary, but most put bounce rate and D1 above monetization and playtime.
- Fix bounce rate and retention before working on monetization, which most call the least important signal.
- Buying your own developer products raises PCR and ARPPU but does nothing for home-rec impressions.
- Robux earned versus ad spend (return on ad spend) does not affect the algorithm.
- Impressions are reportedly calculated from your recent averages, so poor averages mean fewer impressions (low confidence).

---
Related concepts: [[Home Recommendations]] · [[QPTR (Qualified Play-Through Rate)]] · [[Play Days and D28]] · [[Bounce Rate]] · [[D1 Retention]] · [[PTR (Play-Through Rate)]] · [[Playtime]] · [[Thumbnails]]
