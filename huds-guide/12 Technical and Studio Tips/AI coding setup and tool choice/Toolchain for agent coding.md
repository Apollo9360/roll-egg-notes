---
tags: [huds-guide, technical]
---

# Toolchain for agent coding

Up: [[AI coding setup and tool choice]] · Previous: [[Filesystem sync (Rojo and alternatives)]] · Next: [[Which tool (community view over time)]]

1. Install luau-lsp (language server), selene (linter) and stylua (formatter) via rokit.
2. Sync with Rojo and generate a sourcemap so luau-lsp understands the project.
3. Add a Roblox docs MCP and a codebase-memory MCP alongside the Studio MCP.
4. Add hooks forcing the agent to use luau-lsp to find callers instead of guessing.
5. Keep concise architecture .md files; bloated instruction files waste tokens.
