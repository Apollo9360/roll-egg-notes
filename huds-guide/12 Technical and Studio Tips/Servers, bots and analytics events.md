---
tags: [huds-guide, technical]
---

# Servers, bots and analytics events

Up: [[Technical and Studio Tips]] · Previous: [[Performance, loading and mobile optimization]] · Next: [[UI scaling and layout]]

### Server size and places

- Max server size is 200, but ~50 is recommended; big CCU is spread across many servers.
- For small games, lower max size to 16–22 so servers feel full.
- For plot-based games, match max players to the plot count (6 plots, 6 players). One game sat at 6 CCU because 50-player servers had full plots and blocked joins.
- Most experienced developers recommend Roblox-optimized server size, including for PvP and battlegrounds games.
- A lobby that teleports players to another place in the same experience keeps all analytics. Its effect on playtime metrics is unconfirmed.
- For PvP at launch, use same-server matches. Global queues need MemoryStore or external services.

### Bots for multiplayer games

- Never make a game strictly 2-player; allow solo play.
- Add bots or AI opponents in dead servers, and remove them when real players arrive.
- Blade Ball, for example, drops players into an automatic practice match against bots.
- Players may leave after an onboarding bot match, feeling done. Give them a reason to play real players.

### Funnels and custom events

- Implement with [AnalyticsService](https://create.roblox.com/docs/reference/engine/classes/AnalyticsService).
- Tutorial funnel steps must be coded into the tutorial as funnel events, one per step.
- Track only players who have never done the tutorial.
- A big drop between two steps points to a broken or confusing step, such as an "open crates" step.
- Use sub-funnels for finer detail, and error reports as a quick warning signal.
- Funnels show data within a few hours; custom events take about 24 h.
- Custom in-game stat boards give instant playtime and retention data without waiting on Roblox analytics.
- Tutorial completion targets rose: 70–75% was called good in late July 2026; by late August the goal was 80%+, with 60% very bad.

---
Related concepts: [[Onboarding and Tutorials]] · [[Funnels]] · [[Updates and Live Ops]] · [[Playtime]] · [[CCU (Concurrent Users)]] · [[AI Tools]]
