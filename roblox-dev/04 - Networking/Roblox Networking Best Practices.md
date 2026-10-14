---
title: Roblox Networking Best Practices
category: Networking
tags: [networking, remotes, replication, client-server]
status: active
source_quality: AUTHORITATIVE
last_verified: 2026-10-14
aliases: [RemoteEvent guide, Remotes, Client Server Communication]
---

# Roblox Networking Best Practices

## Summary

The server owns game state. Clients send **intent** ("I want to buy Potion x2"), and the server validates it, changes state and replicates the result. Choose the remote type by its delivery guarantee, keep payloads small and infrequent, and treat every client message as hostile.

Security is covered in [[Remote Security and Anti-Exploit]]. A code template is in [[Recipe - Secure Remote Handler]].

## Remote types and their guarantees

| Type | Direction | Delivery | Use for |
|---|---|---|---|
| `RemoteEvent` | C→S, S→C, S→all | **Reliable and ordered**, including across different RemoteEvents to the same peer | Requests, state changes, UI updates, anything that must arrive |
| `UnreliableRemoteEvent` | Same | **No delivery or order guarantee.** Payloads **over 1,000 bytes are dropped.** Over-limit sends are dropped, not delayed. Messages that arrive with no handler connected are discarded. | Continuous, replaceable data: aim direction, cosmetic positions, VFX triggers |
| `RemoteFunction` | C→S→C (S→C→S is risky) | Reliable; the caller yields for the reply | Requests that need an answer: purchase result, validation result |

Facts from the official docs (creator-docs, 2026-10-13):
- **Client→server throttle:** about **500 requests/second per client**, shared by all remotes of the same type (all RemoteEvents together; all UnreliableRemoteEvents together).
- **Over-limit RemoteEvent traffic is delayed, not dropped.** It is processed later and keeps its order.
- **Queued messages:** if no handler is connected yet, RemoteEvent messages and RemoteFunction invocations queue until one connects. The queue is limited; once full, messages are discarded with a `Remote event invocation` error.
- **Ordering:** ordering applies to when handlers *start*. A handler that yields (`task.wait`, `WaitForChild`) can let the next message start first.
- **No ordering between types:** RemoteFunction and RemoteEvent traffic have no guaranteed order relative to each other. If order matters, use one remote type or add a sequence number.
- **Unreliable ordering:** UnreliableRemoteEvent messages can arrive out of order. Include a timestamp or sequence number and ignore older ones.

### Never use `InvokeClient` for anything important

Server→client→server RemoteFunctions are dangerous:
- If the client errors, the server errors too.
- If the client disconnects mid-call, the server call throws.
- If the client never returns, the server **yields forever**.

Use a RemoteEvent to the client and a separate RemoteEvent back, with a server-side timeout.

## What survives the trip (argument rules)

| You send | They receive |
|---|---|
| Function | `nil` |
| Table with a metatable | Plain table; metatable lost |
| Mixed array/dictionary table | Undefined. **Send pure arrays or pure dictionaries.** |
| `nil` holes in arrays | Broken; avoid `nil` values at any index |
| Table with non-string keys (Instance, userdata) | Keys converted to strings |
| Instance the receiver can't see (ServerStorage, or a part made on the client) | `nil` |
| Any table | A **copy**; identity is not kept |
| First parameter on the server | Always the `Player` who sent it. Clients cannot fake it. |

## Core principles

1. **Send intent, not results.** Send `BuyItem("Potion", 2)`, never `SetCoins(500)`. Send `Fire(origin, direction)`, never `Damage(target, 50)`.
2. **The server is a gatekeeper, not a relay.** Before re-broadcasting a client message with `FireAllClients`, validate it and rate-limit it per player.
3. **Fewer, bigger, slower messages beat many small fast ones.**
   - Batch per frame or per tick.
   - Send deltas, not full state.
   - Throttle continuous data to 10–20 Hz on an `UnreliableRemoteEvent`. This rate is a common community choice, not an official number.
4. **Let replication do its job.** Properties and attributes on server-owned instances replicate automatically. Use a remote only for what replication doesn't cover: requests, one-shot events, per-player private data.
5. **One remote per purpose**, created by the server, kept in `ReplicatedStorage` (or a `Remotes` folder there). Don't create remotes from the client; those never replicate.
6. **Connect handlers early**, and make client code tolerate messages that arrive before its UI exists.
7. **Don't pass instance paths** that let a client choose what the server modifies or deletes. Accept IDs from an allow-list instead.

## Implementation pattern

```text
ReplicatedStorage/
  Remotes/              -- created by the server at startup (or authored in Studio)
    Shop/BuyItem        (RemoteFunction)
    Combat/Fire         (RemoteEvent)
    Combat/Aim          (UnreliableRemoteEvent)
  Shared/Validate       (ModuleScript; pure checks, safe to replicate)
ServerScriptService/
  Services/ShopService  (validates and mutates; never replicated)
StarterPlayerScripts/
  Controllers/ShopController (UI → remote calls → render result)
```

The server handler always runs the same order of checks; see [[Recipe - Secure Remote Handler]]:
1. Rate-limit.
2. Check types.
3. Check value ranges and allow-lists.
4. Check context (alive? close enough? owns it? off cooldown?).
5. Mutate state in one step with **no yield between check and write**.
6. Reply or replicate.

## Performance considerations

- Remote traffic shows in the **MicroProfiler network view** and the Developer Console **Network** tab; see [[Performance and Profiling Guide]].
- Replicating many changing properties every frame is a common cause of network spikes. Throttle server writes to replicated properties, or move cosmetic motion to the client.
- `buffer` payloads are compact for high-frequency custom data. The engine compresses some types, so the byte count after encoding can differ from what you expect.
- `StreamingEnabled` cuts initial replication on large maps; see [[StreamingEnabled and Large Worlds]].

## Security considerations

Every remote is an attack surface. See [[Remote Security and Anti-Exploit]] and [[Remote Security Checklist]].

## Common mistakes

- Trusting a price, damage number, cooldown or position the client sent.
- Using `InvokeClient` and hanging a server thread.
- Sending mixed tables, or arrays with `nil` holes.
- Firing an UnreliableRemoteEvent with a payload over 1,000 bytes. It is silently dropped (Studio Output logs the overage).
- Expecting a part created on the client to reach the server (it arrives as `nil`).
- Relaying client messages to every client without validation; this lets an exploiter spam effects on everyone.
- Yielding inside a handler between checking a balance and spending it, which opens duplication races.

## Production checklist

- [ ] Every remote has a documented direction, argument types and rate limit.
- [ ] Every server handler validates types, values, context and rate.
- [ ] No `InvokeClient` on paths that matter.
- [ ] Continuous data uses an UnreliableRemoteEvent, under 1,000 bytes, throttled.
- [ ] Per-player tables are cleared on `PlayerRemoving`.
- [ ] Network usage profiled with several clients (Studio **Server & Clients** test mode, up to 8 clients).

## Related notes

- [[Remote Security and Anti-Exploit]]
- [[Recipe - Secure Remote Handler]]
- [[Decision - Which Remote Type]]
- [[Client Server Golden Rules]]
- [[Network Ownership and Physics Security]]
- [[Data Persistence Guide]]

## Sources

All AUTHORITATIVE, from the Roblox creator-docs repo, state 2026-10-13:
- [Remote events and callbacks](https://create.roblox.com/docs/scripting/events/remote)
- [RemoteEvent API: throttling](https://create.roblox.com/docs/reference/engine/classes/RemoteEvent)
- [UnreliableRemoteEvent API: 1,000-byte limit](https://create.roblox.com/docs/reference/engine/classes/UnreliableRemoteEvent)
- [Securing the client-server boundary](https://create.roblox.com/docs/scripting/security/client-server-boundary)

## Last Verified

2026-10-14
