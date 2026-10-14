---
tags: [huds-guide, technical]
---

# Filesystem sync (Rojo and alternatives)

Up: [[AI coding setup and tool choice]] · Previous: [[Connecting AI to Studio]] · Next: [[Toolchain for agent coding]]

- Agents script better and use far fewer tokens on local files synced with Rojo or Argon than reading Studio through MCP.
- Common pattern: code through Rojo, and switch to MCP only for model, map or property edits.
- Rojo treats files as the source of truth, adds Git version control, and works with VS Code extensions. It lacks live co-editing with another scripter.
- Azul treats Studio as the source of truth; Rojo reportedly caused fewer issues.
- Studio's built-in Script Sync is easier to set up and backwards compatible; its sync direction is disputed.
- VS Code is optional for beginners, but brings Rojo, extensions and cheaper AI tooling.
- Keep a reusable framework or GitHub library of game-agnostic systems, and paste it into each new place before connecting MCP.

---
Related concepts: [[AI Tools]]
