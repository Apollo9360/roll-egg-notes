---
tags: [huds-guide, technical]
---

# Bulk-creating gamepasses and developer products

Up: [[AI coding setup and tool choice]] · Previous: [[AI coding setup and tool choice - Open Cloud automation]]

1. Create an API key with game-pass and developer-product read and write for the experience, plus an IP allowlist and expiry.
2. Store the key outside the project folder.
3. List every item, price and description in a JSON file.
4. POST to `apis.roblox.com/game-passes/v1/universes/<id>/game-passes` and `apis.roblox.com/developer-products/v2/universes/<id>/developer-products`.
5. Support a dry-run mode and skip items that already exist.
6. Write the returned IDs back into the game config.
