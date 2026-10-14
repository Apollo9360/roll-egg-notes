---
tags: [huds-guide, analytics]
---

# Benchmarks and percentiles

Up: [[Analytics and Metrics]] · Previous: [[Data lag and update timing]] · Next: [[Funnels and onboarding analysis]]

- Benchmarks are green, yellow or red percentile bars comparing you with similar experiences.
- "8th percentile" means 92% of experiences do better. The 50th percentile is the median.
- The hover tooltip arrow compares the past week with the week before, not the current window.

| Percentile | Reading | Confidence |
|---|---|---|
| About 10th | Core loop or execution is bad | Low |
| 30–40th | Some games still reached home recs | Low |
| 40–50th everywhere | Reasonable goal during ads; then improve | Medium |
| Under 90th | Still worth improving; no game is 90th on everything | Medium |

- Benchmark tiles reportedly need at least 10 DAU and 10 play hours for 7 consecutive days (low confidence).
- Benchmarks, retention and colours reportedly need about 100 DAU to be accurate. Below that, readings like 13% D1 mislead.
- Benchmarks typically appear after 1 day to 2 weeks. Some games had none after weeks of small ad spend, a sign of too little traffic.
- An overview with no comparisons just means not enough data.
- Early values like "D7 100%", "PTR 0%" or "0% QPTR" are bugs or not yet loaded.
- Some labels look wrong, such as 6.8% shown as 8th percentile while the 50th was 5.82%.
- Benchmark values move. Tycoon's 50th-percentile playtime jumped from 14 to 19 min overnight.
- Genre-based benchmarks mean the same raw number ranks very differently by genre. 9.88% D1 sat low in a crowded genre.
- Reported 50th-percentile playtime is about 36 min in RPG; Simulation is said to have the weakest retention benchmarks.
- From early October 2026, benchmarks compare against "experiences with similar players". These are harder and appear about a month after launch.
- Some found the new values unrealistic, such as 7 Robux per user and 120 min playtime.
- How much genre matters is disputed. Many saw percentiles and even ad CTR change after setting a genre (one went from 1.4% to about 3–5% CTR).
- The owner thinks genre has little algorithm impact, because your feed and comparison set come from similar players.
- A mis-set subgenre matters little because benchmarks use the parent genre. Subgenre can only be changed after a month.

---
Related concepts: [[Genres and Saturation]] · [[Playtime]] · [[CTR (Click-Through Rate)]] · [[D1 Retention]] · [[PTR (Play-Through Rate)]] · [[QPTR (Qualified Play-Through Rate)]] · [[D7 Retention]] · [[Home Recommendations]]
