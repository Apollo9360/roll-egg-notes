---
tags: [huds-guide, analytics]
---

# Retention, playtime and sessions

Up: [[Analytics and Metrics]] · Previous: [[Discovery metrics CTR, PTR, QPTR and bounce rate]] · Next: [[Monetization metrics]]

- D1 counts players who return the calendar day after their first play (day 0). D7 counts those who return on day 7.
- Judge D1 and D7 by the Overview percentile, not the over-time graph, which can show 100% when only the developer played.
- For algorithm health, filter D1 to home-rec traffic (Overview, scroll down) (owner-recommended).
- Ad traffic lowers D1. Home-rec D1 is commonly 2–4x ad D1, for example 4% versus 1%.
- D1 often drifts down during an ad run, such as 10% to 5%, as larger samples replace small early ones.
- After only 2 days of ads, D1 is not real. D1 often rises once ads stop.
- D7 shows noise or 0 until the game is 7+ days old. Readings like 80%, or jumps between 0% and 10%, mean nothing.
- D7 higher than D1 usually means the 7-day window has not refreshed or includes pre-ad players.
- In the first 3 days, judge by playtime, then D1. Wait at least one more day for D1 to settle.
- D1 at 100%, or 11% on day 2 from a handful of players, usually means only you and friends played.
- Different views can show different D1 values for the same game, such as 20% versus 13% (unresolved).
- Since the long-term-retention update, retention aggregates over longer windows, so it updates slowly.

### Weekly patterns

- CCU is usually lower on Mondays as children return to school, with a weekend spike.
- D1 dips every Sunday on many games. One game went 15% to 7% on Sunday, back to 15% by Thursday.
- A late-night CCU peak spanning UTC midnight may inflate D1 (low confidence).

### Reading retention

- Low D1 usually means a bad tutorial, confusing gameplay or a weak core loop. If the loop is bad, only changing the loop fixes it.
- High D1 with low D7 means a good hook with no depth. Add a social reason to replay, not just more content.
- Use playtime and D1 for a new game, and D7 and D28 for long-term health.
- D1 and D7 are the only dashboard stats that AFK rewards or monetization tricks cannot inflate (low confidence).

### Playtime and sessions

- Low playtime: check session time and first-5-min retention. Below about 60–65% at 5 min is an onboarding problem (owner-recommended).
- Reported first-5-min retention ranges from about 19% to 71%. Ad players stay far less than organic ones, for example 36% versus 65%.
- Only about 60 min of playtime per user per day reportedly counts. Target about 30 min sessions rather than AFK marathons.
- D2–7 playtime is reportedly averaged over every day in the window, including zero days (low confidence).
- Average playtime often looks very high right after the first home recs, then settles within a couple of days.
- A 51 min average on day 3 can come from only a few dozen players with 10+ min.
- Daily playtime is normally higher than the 7-day average.
- High average playtime with low session time likely means players are rejoining.
- Good average playtime with weak session time can still hurt QPTR, which depends on session length.
- Short ad spikes and bounced bot or ad traffic drag average playtime down.
- Mobile, PC and console playtime can diverge sharply. Break retention and engagement down by device (owner-recommended).
- Audience age matters. One game's playtime fell from 34 min (21+) to 19 min (9–12) when routed to younger players.
- Teleports to a lobby or reserved servers inside the same experience keep all data, including playtime.
- The real-time "session time by place" tile is a live-window average and swings wildly, such as 61 min to 0.4 min. Ignore it at low CCU.

---
Related concepts: [[D1 Retention]] · [[Playtime]] · [[D7 Retention]] · [[Home Recommendations]] · [[CCU (Concurrent Users)]] · [[Test Bursts and Spikes]] · [[Onboarding and Tutorials]] · [[Updates and Live Ops]]
