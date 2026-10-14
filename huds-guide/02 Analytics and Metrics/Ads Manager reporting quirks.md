---
tags: [huds-guide, analytics]
---

# Ads Manager reporting quirks

Up: [[Analytics and Metrics]] · Previous: [[Funnels and onboarding analysis]] · Next: [[Estimates and projections]]

- Cost can appear before plays, so CPP looks huge until plays update.
- For example, plays frozen at 510 (about $0.10 per play) later updated to 1.3k plays and CPP dropped.
- Large click-to-play gaps, such as 5,746 clicks with 269 plays, are mostly reporting delay.
- Ad play counts may undercount; 60 ad plays alongside 2k visits has been seen.
- Judge a campaign about 48 h after it ends. Open-ended campaigns have no true CPP until then.
- Engagement ads have a learning phase of about 24 h with fewer impressions while audiences are tested.
- Per-creative CTR can be checked per day by setting the date range to "today". Some saw it only after stopping the campaign.

| CPP or result | Reading | Confidence |
|---|---|---|
| About $0.002–0.005 | Very good; $0.002 was seen at 8.5% CTR | High |
| Under $0.01 (about $0.008–0.009) | Target; $0.009 was seen at 3–4% CTR | High |
| About $0.02–0.027 | Already poor; roughly 4–5x too expensive | High |
| $2.25 or more | Extremely bad | Medium |
| 3.3% CTR with $6.5 CPP | Detail page is not enticing | Medium |

- Most say CPP matters more than CTR, because plays are what you buy. CTR is still the biggest input to CPP.
- Two thumbnails with the same 2.6% CTR can differ in CPP. Keep the cheaper one.
- Useful derived metrics are cost per playtime hour, cost per Robux earned and ROAS (return on ad spend).
- One game spent 100k Robux over 7 days to reach 400 CCU at only 0.6 ROAS.
- Custom tracking has suggested only 27% of ad joiners were real people; another claim put bots at 70% (low confidence).
- Recurring Ads Manager bugs include:
    - The "last 28 days" view showing nothing while "last 7 days" works.
    - Spend displayed far above the daily budget while cards are charged correctly.
    - Campaigns falling back to "learning".
    - Impression totals of 150k–300k+ wiped, sometimes restoring on their own.
    - Reporting outages lasting days, as in late July and early August 2026.
- Most developers treat Ads Manager as frequently unreliable and cross-check with Creator Hub.

---
Related concepts: [[Cost Per Play (CPP)]] · [[CTR (Click-Through Rate)]] · [[Ads Manager]] · [[Learning Phase]] · [[Updates and Live Ops]] · [[Playtime]] · [[Plays vs Engagement Objective]] · [[Thumbnails]]
