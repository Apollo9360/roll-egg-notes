---
title: Decision - Which Remote Type
category: Decision Guides
tags: [decision, networking]
status: active
source_quality: AUTHORITATIVE facts + labelled judgement
last_verified: 2026-10-14
aliases: [RemoteEvent vs RemoteFunction, UnreliableRemoteEvent vs RemoteEvent]
---

# Decision - Which Remote Type

```text
Does the sender need an answer?
├─ Yes, client asks server → RemoteFunction (InvokeServer); keep the callback fast and validated
├─ Yes, server asks client → DON'T use InvokeClient (it can hang forever or error).
│                            Use RemoteEvent to the client plus a RemoteEvent back, with a server timeout
└─ No
   ├─ Must arrive, in order (state changes, requests, purchases, UI updates) → RemoteEvent
   └─ Replaceable, continuous, cosmetic (aim, positions, VFX spam), ≤ 1,000 bytes → UnreliableRemoteEvent
                                     (add a sequence number; drop stale messages)
```

**Notes:**
- Client→server traffic shares about **500 requests/s per remote type**. Over the limit, RemoteEvent messages are **delayed**; UnreliableRemoteEvent messages are **dropped**.
- RemoteFunction and RemoteEvent traffic have no order relative to each other.

See [[Roblox Networking Best Practices]].

## Sources

[Remote events and callbacks](https://create.roblox.com/docs/scripting/events/remote), [RemoteEvent API](https://create.roblox.com/docs/reference/engine/classes/RemoteEvent), [UnreliableRemoteEvent API](https://create.roblox.com/docs/reference/engine/classes/UnreliableRemoteEvent). AUTHORITATIVE.

## Last Verified

2026-10-14
