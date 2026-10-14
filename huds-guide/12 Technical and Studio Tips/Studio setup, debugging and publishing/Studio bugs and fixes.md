---
tags: [huds-guide, technical]
---

# Studio bugs and fixes

Up: [[Studio setup, debugging and publishing]] · Previous: [[Studio setup, debugging and publishing - Debug tools]] · Next: [[Publishing and live-game gotchas]]

| Symptom | Cause or fix | Confidence |
|---|---|---|
| Selecting and deselecting instances deletes them | Transient Studio bug that resolved itself; see the [DevForum report](https://devforum.roblox.com/t/selecting-and-deselecting-instances-causes-them-to-disappear/4667103) | Medium |
| "Internal server error" or saving fails | Download a local copy while the Studio cloud is down, then wait | High |
| Camera stuck in the viewport | Press and release right mouse; or Esc then click the viewport; or switch tools (Ctrl+1); or reopen | Low |
| Pixelated text or UI in device emulation | Emulator's lower resolution; looks fine live and on a real phone | Medium |
| Luau language server "413 Result is too large" (3 MB limit) | Raise luau-lsp.studioPlugin.maximumRequestBodySize in VS Code or shrink the include list | Low |
| Official script sync direction | Disputed: early reports said Studio-to-local only; later reports say two-way like Rojo | Low |
