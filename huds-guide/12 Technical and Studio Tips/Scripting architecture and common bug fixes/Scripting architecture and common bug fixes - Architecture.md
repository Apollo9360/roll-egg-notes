---
tags: [huds-guide, technical]
---

# Scripting architecture and common bug fixes - Architecture

Up: [[Scripting architecture and common bug fixes]] · Next: [[Specific bugs and fixes]]

- Use a single client and a single server entry script that load modules: controllers client-side, services server-side.
- Split code into many modules and utilities; avoid a single 10k-line server script, and don't hardcode values.
- Bugs that work only after several playtests are load-order races. Use a module loader, keep modules independent until `.Init()`, and avoid circular requires.
- A module loader freezing on a controller that waits for UI: call `task.spawn(module.Init)` and set an explicit require order.
- Use a Signal library (GoodSignal) instead of bindables to coordinate async modules.
- Common libraries: Signal, Janitor and Promise v4. Get them from the DevForum or [wally.run](https://wally.run), not YouTube.
- Other libraries cited: Chrono for physics replication, Networker for scriptable remotes, and [Flamework](https://flamework.fireboltofdeath.dev/) for TypeScript projects.
- Set an instance's properties before parenting it to workspace, to avoid extra replication cost.
- Debris:AddItem has a hard queue limit of 1,000 items before destroying the oldest early. Use `task.delay(t, obj.Destroy, obj)` instead.
- For latency-sensitive actions, predict on the client and validate on the server.
- Remote config: Open Cloud plus ConfigService (its update event fires ~5 minutes after a change) beats polling DataStores (low confidence).

---
Related concepts: [[Updates and Live Ops]]
