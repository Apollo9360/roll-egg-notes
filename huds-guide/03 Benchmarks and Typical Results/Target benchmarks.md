---
tags: [huds-guide, benchmarks]
---

# Target benchmarks

Up: [[Benchmarks and Typical Results]] · Next: [[How thresholds and rules shifted over time]]

Acronyms: CTR = click-through rate; PTR = play-through rate (home-rec impressions to plays); QPTR = qualified PTR; CPP = cost per play; HEP = highly engaged players (the all-ages reach quota); D1/D7 = day-1/day-7 retention; PCR = payer conversion rate; ARPPU = average revenue per paying user; ARPDAU = average revenue per daily active user; DAU = daily active users; CCU = concurrent users; R$ = Robux; DevEx = Developer Exchange (Robux to cash).

The "Roblox 50th / 90th" column is the creator dashboard's all-genre percentile benchmark, as posted in mid-September. Your own dashboard shows genre-specific percentiles, which can differ a lot.

| Metric | Bad | OK | Good | Great | Roblox 50th / 90th | Confidence | Notes |
|---|---|---|---|---|---|---|---|
| Ad CTR (plays goal) | Under 2% (under 1% very poor) | 2–3% | 3–5% | 5–8%+ (10–17% seen) | ~3.2% median reported once | High | Stop thumbnails under ~2%. A sustained 8%+ on a large sample is rare. |
| Ad CTR sample size | Under ~2k impressions | ~10k impressions per thumbnail |  |  |  | High | 11% on 200 impressions means nothing. |
| CPP (credits per play) | Above 0.02–0.03 (0.08–0.45 worst) | 0.006–0.010 | 0.003–0.005 | 0.001–0.002 |  | High | Common rule: aim for ~0.002–0.005 and stop a thumbnail above ~0.008. |
| Impressions per credit (plays goal) | Under ~5k | 6–10k | 10–15k | 20–33k |  | Medium | High-CTR creatives get fewer impressions per credit. Engagement goal gets 1/3 to 1/10. |
| Ad click to play | Under 20% | 30–40% | 50–60% | 90%+ |  | Medium | Many reports show clicks far above plays (11k clicks for 2k plays). |
| Home PTR / QPTR | Under 1% (0.3–0.7% in stuck games) | 1–2% | 2–4% | 5–8%+ | 1.07% / 2.13% | High | Falls as impressions grow. The owner calls 8% PTR exceptional and ~1.4% poor. |
| Average playtime | Under 5 min | 8–12 min | 15–25 min | 25–45 min+ | 11.6 / 25.0 min | High | Genre matters: 72 min was only the 92nd percentile in an idle genre. Simulation median is ~12 min. |
| Playtime of ad players | Under 5–6 min | 6–10 min | 12–15 min (owner-recommended target) | 16 min+ |  | High | Usually rises ~6 min once ads stop. |
| D1 retention | Under 5% | 7–9% | 10–15% | 18–35% | 7.33% / 12.04% | High | Ads-only D1 is often half of home-rec D1. 15% on ads is excellent. Unreliable under 100 DAU. |
| D7 retention | Under 0.7% | 0.7–1.5% | 2–3% | 4%+ | 0.65% / 1.98% | Medium | A day-7 Robux reward can inflate D7. |
| 5-minute new-user retention | Under 35% | 40–50% | 50–70% | 70%+ (77–90% seen) |  | High | Owner: 50% is decent and 70% is excellent. |
| Bounce rate (first 60 s) | 20–25%+ | 10–15% | 5–10% | Under ~5% |  | Medium | The owner calls 21–25% bad. A load cutscene cut it from 21% to ~14%. |
| Tutorial completion | Under 50% | 55–70% | 75–85% | 90%+ (95–98% seen) |  | High | The target rose to 80%+ over the summer. |
| Like ratio | Under 50% | 50–70% | 70–85% | 85%+ |  | Medium | Ad players rate lower (~60% on ads, 80%+ on recs in one game). |
| PCR | Under 0.2% | 0.4–1% | 1–3% | 4–8% | 0.43% / 2.63% | High | Shopping and paid-access games run far higher (10–20% is normal there). |
| ARPPU | Under 40 R$ | 50–100 R$ | 150–400 R$ | 500–1.4k R$ | 70.3 / 316 R$ | High | Cheap packs raise PCR and lower ARPPU. Deep-progression games reach 1k+. |
| ARPDAU | Under 0.3 R$ | 0.5–2 R$ | 2–5 R$ | 5–20 R$ |  | Medium | Audience region decides most of it. Figures above ~14 are usually inflated. |
| R$ per visit | Under 0.3 | 0.5–1 | 1–2 | 2+ (incrementals) |  | Medium | About 1 R$ per visit is the common mark of good monetization. |
| R$/day per average CCU | Under 10 | 20–50 | 70–100 | 100–300+ |  | Medium | See the region table in Estimates and projections. |
| Visits before home recs |  | 10–20k | Fewer with strong stats |  |  | Medium | Some games reached recs on a few thousand impressions. |

- The median benchmarks above line up closely with the community's "OK" band, which raises confidence in both.
- Genre percentile examples: 15% D1 was the 99th percentile in survival; 16.5% D1 was the 86th in another genre. Quoted genre targets are ~12% D1 for simulators and ~18% for adventure.
- A strict 20%+ D1 rule was refuted: games held 12k CCU at 17% D1 and 20k CCU at 10–15% D1.
- An "investment-grade" stat line looks like 45 min playtime, 30–35% D1, 8% PCR and high ARPPU.
- Long-lived classics reportedly run 50–60% D1. In earlier years, 5% D1 could still reach the algorithm; that no longer holds.

---
Related concepts: [[D1 Retention]] · [[Genres and Saturation]] · [[CTR (Click-Through Rate)]] · [[PTR (Play-Through Rate)]] · [[D7 Retention]] · [[Payer Conversion]] · [[Home Recommendations]] · [[CCU (Concurrent Users)]]
