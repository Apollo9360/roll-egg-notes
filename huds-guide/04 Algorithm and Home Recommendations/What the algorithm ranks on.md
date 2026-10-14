---
tags: [huds-guide, algorithm]
---

# What the algorithm ranks on

Up: [[Algorithm and Home Recommendations]] · Next: [[Why ads come first]]

Engagement consistently outranks monetization, though monetization gained weight after the August 6 update. Since mid-June the core signals are PTR, first-play bounce rate, retention from D1 to D28, playtime and play days.

| Signal | What it measures | Typical weight | Confidence |
|---|---|---|---|
| PTR (play-through rate) | Share of home-rec impressions that become plays; mostly thumbnail, name and idea | Often called the main bottleneck; a very low PTR caps a game with otherwise strong stats | High |
| First-play bounce rate and early churn | Whether new players stay past the FTUE (first-time user experience) | Top tier; drives how fast exposure grows before D7 data exists | High |
| Retention (D1, D7, D8–28, D30) | Share of players returning after 1, 7 or more days | Gates entry; long-term retention weighted more since June | High |
| Average playtime and session time | Time per player and per session | Top tier; little extra value past about 60 minutes | High |
| Play days per user | Days per player with a session | Named by Roblox among the first signals to fix | High |
| Co-play | Friends inviting and playing together | Rewarded since June; size of effect unclear | Medium |
| Monetization (ARPPU, PCR, ARPDAU) | Average revenue per paying user, payer conversion rate, revenue per daily user | Secondary; gained weight in August; exact weight disputed | Medium |
| Like ratio and favorites | Thumbs-up share | No ranking effect; a very low rating may dent clicks | Medium |

- QPTR (qualified play-through rate) originally blended thumbnail and onboarding quality.
- In the June update Roblox split QPTR into PTR (thumbnail and landing quality) and first-play bounce rate (FTUE quality).
- CCU means concurrent users. HEP means highly engaged players.
- Benchmarks most worth watching: PTR, bounce rate, D1, D7, playtime, payer conversion and ARPPU.
- Official docs say engagement matters more than co-play and monetization. See [Roblox discovery docs](https://create.roblox.com/docs/discovery#key-signals).
- Roblox's own algorithm webinar is the best primary source (owner-recommended). See the [explainer video](https://youtu.be/B3R1ybI3yzs).
- The webinar's order: fix PTR, playtime, bounce rate and play days per user first, then the rest.
- The algorithm reads stats relative to similar games, as an aggregate. Be competitive on PTR, playtime and D1, and above the 50th percentile elsewhere.
- Every home-rec signal that is below par lowers home impressions.
- The algorithm does not understand your premise. It only reads stats.
- If players join and leave immediately, recommendations stop.

Sub-sections:

- [[Retention and the 28-day window]]
- [[What the algorithm ranks on - Playtime and monetization]]
- [[Entry gates and position]]

---
Related concepts: [[PTR (Play-Through Rate)]] · [[Playtime]] · [[Bounce Rate]] · [[Play Days and D28]] · [[D1 Retention]] · [[Home Recommendations]] · [[Onboarding and Tutorials]] · [[QPTR (Qualified Play-Through Rate)]]
