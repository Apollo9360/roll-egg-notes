---
name: creator-hub-user-does-it
description: "User creates Creator Hub items (dev products, game passes) themselves; give them a list, then paste the IDs they send"
metadata:
  node_type: memory
  type: feedback
  originSessionId: d723eaa0-ed81-4b9b-bff2-dbdac7a03b61
  modified: 2026-10-13T06:39:15.080Z
---

Don't click through Creator Hub in Chrome to create developer products or game passes. Give the user a ready-to-copy list (name, description, price, Config key) and paste the IDs they send back into `ReplicatedStorage.Shared.Config.Products`.

**Why:** On 2026-10-12 the user stopped me mid-way through creating the first dev product in Chrome and asked for "a list of stuff to set up" instead, even after saying "full permissions".

**How to apply:** For any account-side setup (products, passes, badges, place settings), give the user the list and let them do it. Related: [[no-playtesting]].
