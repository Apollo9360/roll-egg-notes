---
tags: [huds-guide, algorithm]
---

# Test bursts, spikes and crashes

Up: [[Algorithm and Home Recommendations]] · Previous: [[When home recs start]] · Next: [[Getting re-tested and recovering]]

How Roblox tests a game, as described by developers who have turned around several games:

1. Roblox sends a home-rec spike to a small cohort.
2. If those players show bad stats, it runs another, smaller test.
3. If they show good stats, the next spike is bigger, growing roughly exponentially.
4. This repeats until traffic is consistent.

- Before D7 data exists, growth leans heavily on PTR and first-play bounce rate.
- Each new test needs better stats than the previous one to grow.
- Home recs rubber-band: the algorithm tests a pocket of audience, pulls back, then tests again. This is normal.
- Early tests are often 3k–5k recs after 7–10 days of ads.
- Ramps are gradual, for example about 220 to 840 home-rec plays day over day, or 3k to 10k to 60k impressions a day.
- A 10x jump in home impressions is a strong sign of algorithm entry.
- Higher home recs mean tougher competition for your stats, until the game wins or dies.
- At 50–60 CCU you usually have some home-rec players. You want at least 20 consistent ones for enough data.
- There are no small windows. The game is tested continuously, and a good update can trigger a push later.
- A sudden traffic spike with zero home impressions can also be Roblox testing the game.

### Normal spike patterns

| Pattern | Typical numbers | What it means | Confidence |
|---|---|---|---|
| Day-1 ad spike | 100–160 CCU, back to 0 within an hour | Ads system still finding an audience; can include bots | High |
| Post-campaign drop | About 70 to 25 CCU after ads stop | Ad players do not return without great stats | Medium |
| Short test after ads end | About 30 to 0 CCU after 3 days | Roblox tested, then pulled out | Medium |
| Enter then exit recs | 650–1.4k CCU collapsing to 5–20 | Entered recs, then removed; often unexplained | High |
| High-PTR spike | Fades within about 30 minutes | Low playtime or retention; fix them to hold CCU | Medium |
| All-ages unlock spike | 3–4x CCU for about a day, then reverts | Wider-audience test | High |
| Afternoon spike | 150 to 2k to 150 within hours | Cohort test | Medium |
| Home impressions swing | Down 70%, then back up | Competitors' daily performance | Medium |

- Early home-rec numbers are volatile and dip day to day.
- Home recs can drop on day 3. Give it 5–6 days; good retention data makes impressions climb sharply.
- A big home-impression peak followed by a deep drop is a bad sign.
- If impressions drop and bounce rate rises with no game change, Roblox is likely testing an off-target cohort.
- Tests on a new demographic can show as a sudden CCU jump with lower revenue.
- PTR naturally falls as reach grows. Roblox moves to broader, lower-intent audiences and repeats impressions to the same users.
- Wider reach brings more dislikes from people outside the genre.
- Daily swings of ±30% are normal, and larger ones happen. CCU dips at night and while players are in school.
- Spikes are normal after 8 pm Eastern and on weekends; summer changes the pattern.
- A game with few US players may peak overnight US time.
- First-week ads can bring bot traffic, for example 50 CCU that is 95% bots, gone within an hour.

---
Related concepts: [[CCU (Concurrent Users)]] · [[Test Bursts and Spikes]] · [[Home Recommendations]] · [[PTR (Play-Through Rate)]] · [[Bounce Rate]] · [[D7 Retention]] · [[Playtime]] · [[Age Gate and All-Ages Reach]]
