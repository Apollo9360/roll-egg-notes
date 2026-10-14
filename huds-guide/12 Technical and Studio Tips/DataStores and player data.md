---
tags: [huds-guide, technical]
---

# DataStores and player data

Up: [[Technical and Studio Tips]] · Previous: [[Scripting architecture and common bug fixes]] · Next: [[Performance, loading and mobile optimization]]

- Use ProfileStore (not the older ProfileService) plus Replica to replicate data to clients, after learning basic DataStores ([ProfileStore](https://devforum.roblox.com/t/profilestore-save-your-player-data-easy-datastore-module/3190543), [Replica](https://devforum.roblox.com/t/replica-server-to-client-state-replication-module/3216980)).
- [DataService v2](https://devforum.roblox.com/t/dataservice-v2-a-powerful-yet-simple-data-storing-solution/2083899) is an alternative.
- Hand-rolled DataStores tend to lose data once you pass ~100 CCU.
- One DataStore bug can kill a game. If AI wrote the save code, you must be able to read it.
- Some advise never letting Codex write DataStore code; others say newer models handle it fine (disputed).
- Session locking matters: a save retry must never overwrite newer progress.
- Writing currency to a DataStore every second per player lags the server. Batch writes and flush periodically.
- 50–80 bytes per player is generally assumed fine.
- Roblox right-to-erasure requests ask you to delete specific player IDs from DataStores. They are common.

### Global "X exist" counters

1. Keep the live count in MemoryStore, which lacks DataStore's tight rate limits.
2. Flush to a DataStore every 10–20 minutes or on server close.
3. For display only, fetch every 5–10 minutes.
4. Push the value to clients with remotes.
5. Expect MemoryStore entries to persist up to about 30–45 days.

---
Related concepts: [[Learning Phase]] · [[CCU (Concurrent Users)]] · [[AI Tools]]
