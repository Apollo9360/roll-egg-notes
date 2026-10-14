---
tags: [huds-guide, analytics]
---

# Metric glossary

Up: [[Analytics and Metrics]] · Next: [[How the home-recommendation algorithm reads your game]]

| Metric | What it measures | Where to find it | Notes | Confidence |
|---|---|---|---|---|
| Impressions | People who saw the thumbnail without just scrolling past, clicked or not | Ads Manager; Acquisition | Dip at night in your main audience's time zones, such as Brazil and USA | High |
| CTR (click-through rate) | Share of impressions that get clicked; 10% is about 10k clicks per 100k impressions | Ads Manager; click the campaign name for per-creative CTR | Exists only while ads run; falls naturally as impressions grow | High |
| PTR (play-through rate) | Share of home-rec impressions that turn into a play (joins divided by impressions) | Acquisition > Home Recommendations, scroll down | Driven by thumbnail, icon, title and description; shown as a 7-day moving average | High |
| QPTR (qualified play-through rate) | Share of home-rec impressions that become a qualified play of a few minutes | Thumbnail Performance view; Overview | Effectively PTR plus bounce rate; no longer an algorithm signal since June 15, 2026 | High |
| First-play bounce rate | Share of first-time players who leave within 60 s; a second bucket covers 61–180 s | Home Recommendation Signals panel, scroll down | Lower is better; can be split by age group | High |
| Impressions CTR and Detail Page CTR | Thumbnail click rate versus detail-page play rate | Table at the bottom of the Acquisition page | The weaker one shows whether to fix the thumbnail or the page | Medium |
| New user first-session retention | Share of new players still playing after 5 min | Analytics > Retention | Target about 60–65% (owner-recommended) | High |
| D1 / D7 / D28 retention | Share of new players who return 1, 7 or 28 days after their first play (day 0) | Overview; Analytics > Retention | D7 is meaningless before the game is 7 days old | High |
| Play days per user | Days per user with at least one play inside the signal window | Home Recommendation Signals | Called the most important retention signal by the owner | High |
| Average playtime | Total minutes per user per day, summed across all sessions | Overview; Engagement | Two 6-min sessions count as 12 min of playtime | High |
| Session time | Length of one session, join to leave | Engagement; Session Time by Place | Always at or below daily playtime | High |
| PCR (payer conversion rate) | Share of players who buy anything | Monetization; viewable by country | Easily gamed by 1–3 Robux items; inflated by developer purchases | High |
| ARPPU (average revenue per paying user) | Revenue divided by paying users | Monetization | One buyer can swing it on small samples | High |
| ARPDAU (average revenue per daily active user) | Revenue divided by DAU; equals PCR × ARPPU; also called RPV (revenue per visit) | Monetization > Overview, scroll to the bottom | The most useful single monetization number | High |
| Robux spent per user | Average Robux spent by home-rec users; effectively home-rec ARPDAU | Home Recommendation Signals | One of the six spend-related signals | Medium |
| HEP (highly engaged players) | Players who met a playtime threshold and spent Robux anywhere on Roblox in the past 60 days | Overview > Audience reach; Manage audience page | Rolling window; lags 24–48 h; not the same as visits | High |
| CPP (cost per play) | Ad spend per player who actually plays | Ads Manager | Good is about $0.002–0.01; lags up to 48 h | High |
| CCU (concurrent users) | Players in the game at one moment | Monitoring > Performance | Near real-time; the peak figure can take days to become accurate | High |
| DAU and MAU (monthly active users) | Unique users who opened the game that day or month | Overview; Open Cloud analytics | 1.8k DAU with about 10 average CCU is normal | Medium |
| Funnel step % | Share of the previous step's users who completed this step | Analytics > Funnels | Relative to the previous step, not to step 1 | High |
| Feedback count | Likes, dislikes and written player feedback | Analytics, Feedback section; written feedback under Audience | Count includes ordinary likes and dislikes | Medium |

- "Qualified play sessions per user" is one of the official signals, but its exact definition is unpublished.
- A "not interested" rate appears with home-rec impressions. Whether it is per thousand or per million impressions is unclear (low confidence).
- Stickiness (DAU divided by MAU) means little on a new game, where a high daily share is natural. One game reported about 16.5%.

---
Related concepts: [[Home Recommendations]] · [[Playtime]] · [[CTR (Click-Through Rate)]] · [[Thumbnails]] · [[PTR (Play-Through Rate)]] · [[QPTR (Qualified Play-Through Rate)]] · [[Ads Manager]] · [[Payer Conversion]]
