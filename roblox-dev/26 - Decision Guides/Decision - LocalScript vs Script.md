---
title: Decision - LocalScript vs Script
category: Decision Guides
tags: [decision, architecture]
status: active
source_quality: AUTHORITATIVE facts + labelled judgement
last_verified: 2026-10-14
aliases: [LocalScript vs Script, RunContext]
---

# Decision - LocalScript vs Script

| Code | Use |
|---|---|
| Server logic | `Script` (RunContext Legacy or Server) in **ServerScriptService** |
| Client logic (recommended) | One `Script` with **RunContext = Client** in ReplicatedStorage as the client entry point, requiring ModuleScripts |
| Client logic (classic) | `LocalScript` in StarterPlayerScripts, StarterCharacterScripts (re-runs on each spawn), StarterGui or StarterPack |
| Shared code | `ModuleScript` in ReplicatedStorage. **No secrets** (it can be decompiled). |

**Don'ts:**
- A RunContext=Client Script in `Starter*` containers runs twice (original plus copy).
- Avoid scripts inside Workspace models; use tags plus one module.

See [[Roblox Project Architecture]].

## Sources

[Script types and locations](https://create.roblox.com/docs/scripting/locations). AUTHORITATIVE.

## Last Verified

2026-10-14
