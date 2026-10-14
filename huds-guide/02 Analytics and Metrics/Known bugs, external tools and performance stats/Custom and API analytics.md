---
tags: [huds-guide, analytics]
---

# Custom and API analytics

Up: [[Known bugs, external tools and performance stats]] · Previous: [[Likes and external estimates]] · Next: [[Known bugs, external tools and performance stats - CCU patterns]]

- Lag workarounds include GameAnalytics, Discord webhooks logging each join and leave, and in-game admin panels tracking per-player playtime.
- An open-source real-time tracker with about a 5 min delay exists. Custom systems help catch D1 changes faster.
- Batch events in buffers and send one large HTTP request per minute to stay within limits. One system cost $100–500 a month at 1k CCU.
- Datastore-based A/B experiments can test features against playtime across your player base.
- The Open Cloud `universe.analytics:read` scope gives read-only DAU, MAU, revenue, retention and acquisition, filterable by country, platform and age.
- Scope the key to one experience. AI assistants can then scan your stats and suggest what to fix.
- Pair analytics with commit history to tie each drop to a specific update.

---
Related concepts: [[Playtime]] · [[Updates and Live Ops]] · [[D1 Retention]] · [[CCU (Concurrent Users)]] · [[AI Tools]]
