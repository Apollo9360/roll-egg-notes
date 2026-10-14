---
tags: [huds-guide, analytics]
---

# Data lag and update timing

Up: [[Analytics and Metrics]] · Previous: [[Daily view vs 7-day average, and sample size]] · Next: [[Benchmarks and percentiles]]

| Data | Typical lag | Confidence |
|---|---|---|
| Creator Hub analytics overall | 24–72 h; Day X usually arrives around Day X+2; owner says about 3 days | High |
| Visits and CTR | Near immediate | Medium |
| Retention, playtime and session stats | 2–3 days; occasionally 3–6 days | High |
| "Settled" stats after ads start | Inflated readings about 3 days in; realistic after 5–6 days | Low |
| Play counts | 1–2 days | High |
| HEP counter | 24–48 h; counts once per UTC day; can stall for days | High |
| Benchmarks after release | 1–3 days typical; one to two weeks for low traffic | Medium |
| Similar-player benchmarks | About a month after launch | Medium |
| Funnels | Event data within 2–5 h of players arriving; new funnels and custom events about 24 h | Medium |
| CCU | Real-time in Monitoring > Performance | High |
| Thumbnail stats in Ads Manager | 30–60 min to appear; about 7 h to update; up to 2 days in bad weeks | Medium |
| Ad plays and CPP | Up to 48 h, sometimes about 3 days | High |
| Ads Manager overall | Refreshes about once a day; 2–3 days to settle | High |
| PTR after a thumbnail change | Slower than other stats | Medium |

- Stats can change completely as data fills in, so wait before acting.
- If funnel data shows nothing about 5 h into an ad run, check your event code.
- The HEP panel shows a "last updated" time. It can stall for days; in late September and early October 2026 it froze platform-wide for 2+ days.
- Early algorithm stalls are often just D7 not populated yet.
- A custom funnel may show 60% staying 30 min while the lagging dashboard still shows 6 min average playtime.

---
Related concepts: [[Updates and Live Ops]] · [[Funnels]] · [[Playtime]] · [[HEP (Highly Engaged Players)]] · [[Thumbnails]] · [[Ads Manager]] · [[CTR (Click-Through Rate)]] · [[PTR (Play-Through Rate)]]
