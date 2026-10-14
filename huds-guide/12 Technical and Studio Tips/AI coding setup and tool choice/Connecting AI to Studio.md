---
tags: [huds-guide, technical]
---

# Connecting AI to Studio

Up: [[AI coding setup and tool choice]] · Next: [[Filesystem sync (Rojo and alternatives)]]

1. In Studio, open the Assistant, choose "Manage MCP servers" (or the three dots, then MCP), and enable Studio as an MCP server.
2. Use Quick Connect, or paste the command or JSON into Claude Desktop or Claude Code.
3. For Codex on Windows, launch cmd.exe with "/c" and the CLI line as arguments.
4. If MCP disconnects, restart or force-quit Claude, or toggle MCP in Studio; failing that, kill both in Task Manager and reconfigure.

- Official docs: [Studio MCP](https://create.roblox.com/docs/studio/mcp). The legacy standalone Rust MCP server repository is reportedly deprecated.
- The built-in Studio MCP gives the agent only the Edit context.
- [robloxstudio-mcp](https://github.com/Chrrxs/robloxstudio-mcp) adds client and server runtime plus multiplayer playtests. Turn off Studio's "enable as MCP server" when using it.
- Other community servers: [mcp-roblox-docs](https://github.com/n4tivex/mcp-roblox-docs) for documentation lookup, and [Dominus 2](https://devforum.roblox.com/t/dominus-2-an-mcp-server-for-roblox-studio-46-tools/4833955) with 46 tools.
- Setup video: [Roblox MCP with Claude Code](https://www.youtube.com/watch?v=4jvjtfB0AQU); it exposes about 48 tools.
- Turn off Collaborative Editing while using MCP; a bug there can kill the session and its data. Keep file backups.
- Studio updates have broken MCP connections, and connections slowed from ~20 s to ~1 min for some.
- Running several agent tasks against one Studio at once drops the connection.
- Studio's built-in Assistant handles grunt work like searching the tree, but is weak overall. It once deleted everything with no undo.
- Third-party Studio assistants such as Ropilot are considered mediocre; the free MCP plus Claude Code is enough (owner-recommended).

---
Related concepts: [[AI Tools]] · [[Updates and Live Ops]]
