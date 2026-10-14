---
title: StreamingEnabled and Large Worlds
category: Performance
tags: [streaming, streamingenabled, large-worlds, memory, slim, replication-focus]
status: active
source_quality: AUTHORITATIVE
last_verified: 2026-10-14
aliases: [Instance streaming, StreamingEnabled, ModelStreamingMode, Replication focus]
---

# StreamingEnabled and Large Worlds

## Summary

Instance streaming loads and unloads **Workspace** content around each player. It gives faster joins, lower memory use and better FPS, especially on mobile. It is **on by default** in new places, can only be set in Studio (not by scripts), and requires client code that **tolerates parts appearing and disappearing**.

## How it behaves

Official facts:
- **Scope:** only Workspace descendants stream. ReplicatedStorage and ReplicatedFirst contents always replicate fully.
- **On join:**
  - non-part Workspace instances replicate;
  - BaseParts, and Atomic, Persistent or PersistentPerPlayer models, are deferred and streamed in by distance and device conditions;
  - with `ModelStreamingBehavior = Improved`, nonatomic models are deferred too.
- **Stream out:**
  - regions furthest from the replication focus go first;
  - nothing inside `StreamingMinRadius` streams out;
  - streamed-out instances are **parented to nil, not destroyed**. `ChildRemoved` and `DescendantRemoving` fire on ancestors, and they come back if streamed in again.
- **Exempt from stream-out:**
  - instances created or cloned by **client** scripts, unless parented under a server-created instance;
  - models with no BaseParts, used as containers.
- **Assemblies** stream in as a whole (with constraints and attachments). They stream out only when every part is eligible.
- **Physics:** client-side simulation, including server-authority prediction, only happens in streamed-in areas.

## Model streaming modes

| Mode | Behaviour | Use |
|---|---|---|
| Nonatomic (default) | Parts stream individually | Most world content |
| **Atomic** | All initial descendants arrive together; `WaitForChild` on the model only | Doors, machines, anything scripts need whole |
| **Persistent** | Sent soon after join, before `Workspace.PersistentLoaded`, never streams out | Rare: a few parts that client scripts must always see. Not a way around streaming. |
| PersistentPerPlayer | Persistent for players added with `Model:AddPersistentPlayer()`, Atomic for everyone else | A player's own plot or base |

## Key properties

Set them on Workspace in Studio:
- `StreamingMinRadius` and `StreamingTargetRadius`. Keep target **larger** than min; the gap is a buffer. Lower both to save memory more aggressively.
- `StreamOutBehavior`.
- `StreamingIntegrityMode = PauseOutsideLoadedArea` (recommended). The game pauses if the player walks into an unloaded area. `Player.GameplayPaused` lets you show a custom pause UI.
- `PredictiveStreamingMode = Enabled` (opt-in) pre-streams where the player is likely to go next. It is additive and needs no code changes.
- `Player.ReplicationFocus`, `Player:AddReplicationFocus()` and `RemoveReplicationFocus()` stream other areas, such as home base or a trading hub. **Each extra focus adds server load** comparable to another moving player.
- `Player:RequestStreamAroundAsync(position)` pre-fetches before a teleport or cutscene.
- **SLIM:** `Model.LevelOfDetail = SLIM` and `Workspace.EnableSLIMAvatars` give lightweight distant representations.

## Writing streaming-safe code

1. Never assume a Workspace part exists on the client. Use `WaitForChild` with a timeout, or better, **CollectionService added/removed signals**. They fire as tagged instances stream in and out.
2. Clean up when an instance streams out: disconnect, stop effects. See [[Cleanup Pattern (Maid Janitor Trove)]].
3. Keep server logic on the server, which always sees the whole world.
4. Before a client-visible event at a far location (teleport, cutscene), call `RequestStreamAroundAsync` first.
5. UI that references world objects (minimap markers, nametags) must handle missing objects gracefully.

## Debugging

- Shift+F3 (live) or Shift+Ctrl/⌘+F3 (Studio) opens network stats. Press Shift+1 until the **Streaming** panel shows loaded regions in the viewport.
- Temporarily raise `CameraMaxZoomDistance` (e.g. 1000) to watch regions load.

## Common mistakes

- Turning streaming off to "fix" nil errors instead of making client code tolerant.
- Marking large parts of the map **Persistent**.
- Too many replication foci.
- Client scripts holding references to streamed-out parts. The parts are parented to nil, not destroyed, so connections on them stay alive.

## Related notes

- [[Performance and Profiling Guide]]
- [[Roblox Project Architecture]]
- [[Decision - When to Use StreamingEnabled]]

## Sources

- [Instance streaming](https://create.roblox.com/docs/workspace/streaming), [Streaming techniques](https://create.roblox.com/docs/workspace/streaming/techniques), [SLIM](https://create.roblox.com/docs/workspace/streaming/slim). AUTHORITATIVE, creator-docs 2026-10-13.

## Last Verified

2026-10-14
