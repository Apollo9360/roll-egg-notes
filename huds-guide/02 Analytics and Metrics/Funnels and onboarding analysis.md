---
tags: [huds-guide, analytics]
---

# Funnels and onboarding analysis

Up: [[Analytics and Metrics]] · Previous: [[Benchmarks and percentiles]] · Next: [[Ads Manager reporting quirks]]

- Onboarding is the first 3 min: the tutorial and early gameplay. FTUE (first-time user experience) covers the first few sessions.
- Funnels need custom events via [AnalyticsService](https://create.roblox.com/docs/reference/engine/classes/AnalyticsService). See the [funnel events guide](https://create.roblox.com/docs/production/analytics/funnel-events) and the [funnel and economy events post](https://devforum.roblox.com/t/analytics-track-funnel-and-economy-events-to-grow-your-experience-faster/3060398).
- AI coding tools can wire funnel events in minutes (owner-recommended).
- Each step's % is relative to the previous step. 91% on step 2 means 91% of step-1 users finished it.
- Track every small step (owner-recommended). One team split a 7-step funnel into about 15 steps and found the problem.

1. Add a funnel event at each tutorial step, aiming for 12+ steps.
2. Optionally track only players who have never done the tutorial.
3. Add milestone steps past the tutorial, such as first rebirth or zone 2, until you cover 15–30 min. Add a step near 1 h if useful.
4. Wait at least a day, or until churn is clear; the sample need not be huge.
5. Find the single biggest drop, then add intermediate steps around it to locate the exact point.
6. Form a hypothesis for why players leave there, fix it, then repeat (owner-recommended).

| Funnel reading | Verdict | Confidence |
|---|---|---|
| Tutorial completion about 85% | Target | Medium |
| 70–80% | Common target range; minimum a tutorial should keep | Medium |
| 68–70% | Not good enough, even for a 1–1.5 min tutorial | Low |
| 25–50% | Common on first games; needs work | Medium |
| Step drop 0–5% | Very good | Low |
| Step drop 5–10% | Normal | Low |
| Step drop 10–15% | Worth a look | Low |
| Step drop 20%+ | High, especially on step 1 | Medium |

- Act on any step with a clear drop. A 6% drop at a welcome step is significant, and 16% on step 1 is abnormal.
- Total completion depends on length. A 3-step funnel can exceed 90%, while an hour-long one is normally about 10%.
- Judge the drop between steps, not only total conversion.
- Tutorial completion counts only players who did not bounce first. Check the 61–180 s bounce bucket too.
- Tutorial completion usually rises once home-rec players replace ad players, for example from 75% to 89%.
- Example finds include an arrow hidden under a dialog, a big drop at "open crates", and 30% churn at a 2-player obby checkpoint.
- One funnel showed players who missed zone 2 averaged 16 s of playtime, while those who reached it averaged about 22 min.
- One tablet-only step had 60% churn and dragged down average playtime. Sort the funnel by device.
- Reported funnel fixes cut first-tutorial drop from 40% to 15%, cut 5-min churn from 50% to 40%, and lifted new-player retention about 60%.
- Use progression milestones, not time buckets. "Left at 10–15 min" does not tell you what happened.
- For combat games, log matches, kills, deaths and leave time.
- If a later step fires, all earlier steps auto-trigger. Make sure a final event cannot fire alongside a mid-funnel one.
- Changing step order corrupts old data with no reset. Give the restructured funnel a new name.
- Funnels from an old game stay when you replace it with a new game in the same place.
- A spectate system lets you watch real players and see where they drop off.
- The Feedback tab shows player-reported issues. Use it during ad testing to find bugs; add an in-game feedback button too.

---
Related concepts: [[Funnels]] · [[Onboarding and Tutorials]] · [[Updates and Live Ops]] · [[Bounce Rate]] · [[Playtime]] · [[Core Loop and Progression]] · [[Home Recommendations]] · [[AI Tools]]
