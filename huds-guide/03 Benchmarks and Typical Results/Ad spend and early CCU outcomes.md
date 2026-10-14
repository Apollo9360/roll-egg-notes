---
tags: [huds-guide, benchmarks]
---

# Ad spend and early CCU outcomes

Up: [[Benchmarks and Typical Results]] · Previous: [[How thresholds and rules shifted over time]] · Next: [[Impressions, cost per play and click-to-play]]

| Ad spend level | Typical CCU during ads | Range seen | What decides it | Confidence |
|---|---|---|---|---|
| Tiny test (1–6 credits) | Brief spike, then near zero | 27 visits to 1k visits; spikes of 40–200 CCU lasting minutes to an hour | CTR. Early spikes are learning-phase noise. | High |
| 5/day for 7–14 days | 5–20 CCU, few home recs | 160 HEP and no recs on 50 credits; a stable 20 CCU in better cases; rare trend games broke out | Too little data for the algorithm, though $12 over 9 days reached recs once | Medium |
| 8–12/day | 10–30 CCU | 10–15 average typical; steady 100–120 on a strong game | PTR and playtime | High |
| 16/day (standard) | 10–40 CCU (median ~20–30) | 1–2 CCU on weak games; 150–200 held on strong ones; first recs on days 3–5 with good stats | CTR, playtime and thumbnail count | High |
| 20–30/day | 15–100 CCU | 100 average by day 2 and 200–250 by day 4 at ~4% CTR; 9–10 CCU on a fresh weak game | Mostly PTR and retention | Medium |
| 40–50/day | Fastest quota and recs, not proportionally more CCU | Recs on days 2–3; ~830 peak at $50/day | Onboarding and core loop | Medium |
| $150–250/day | Rarely worth it | $200/day for a week added only ~300 CCU over home-rec CCU (owner); $250/day could not hold 100 | Game quality, not budget | High |
| Long runs (3+ weeks, $200–600) | Rarely rescue a weak game | 600+ credits over 20 campaigns stayed under 10 CCU | Bounce rate and PTR | High |

### Rules of thumb

- Run ads 7–10 days. Most call 4 days too short to judge.
- A common plan: 16/day for 7 days, then ~10/day for 4 days after recs start (owner-recommended).
- Another plan that "usually works": 10/day for a week, then 5/day for 4 days, about 90–100 credits in total.
- The owner estimates $100–250 of ads to reach the algorithm, depending on stats. Games with ~30% D1 got there on about $100.
- Spending more does not grow a weak game. The owner spent ~$800 to prove this to a developer.
- $30 total is not enough (owner). A launch budget of about $100 is safer than $40–50.
- Stop ads once home recs carry the game. Some stop at ~100k home-rec impressions per day.
- Drop a game after $300–400 spent with no traction, and iterate instead.
- Ad traffic arrives in bursts. Read little into CCU during ads.
- A first campaign on a fresh game often spikes 100–2k CCU for 10–60 min, then settles near 30. Later campaigns rarely repeat it.
- A spike of 200 that takes 40 min to fall to 20 is a good sign. Spiking above 100 at all is positive.
- After ads end, roughly 10% of ad CCU remains unless home recs have started. Drops of 200 to ~10 and 300 to ~30 are typical.
- Home recs often start 3–10 days after ads begin, sometimes after ads end.
- After reaching the HEP quota, a fresh plays campaign can trigger pickup (3–10 to 200+ CCU in one game).
- Ad players lower stats. Stopping ads raised playtime from ~7 to ~20 min in one game and from 40 to 80–100 min in an incremental.
- Incrementals get little from ad players and depend on home recs, so they need all-ages reach.
- For a game already on home recs, ads add little CCU and lower stats. A minority found profitable ads still worth running at 5/day.
- Games rated 16+ get ads shown to kids and very few home recs.
- Ads skew to mobile: a PC-oriented game drew 96% mobile players, who played 4.6 min versus 15 min on PC.
- Day-1 revenue on a fresh campaign is usually near zero. Revenue during ads typically returns 30–50% of spend; ads rarely pay back before recs.

---
Related concepts: [[CCU (Concurrent Users)]] · [[Home Recommendations]] · [[Test Bursts and Spikes]] · [[Ads Manager]] · [[Ad Credits]] · [[CTR (Click-Through Rate)]] · [[PTR (Play-Through Rate)]] · [[Playtime]]
