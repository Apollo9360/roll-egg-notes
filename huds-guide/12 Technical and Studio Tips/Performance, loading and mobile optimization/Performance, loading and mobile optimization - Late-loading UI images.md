---
tags: [huds-guide, technical]
---

# Performance, loading and mobile optimization - Late-loading UI images

Up: [[Performance, loading and mobile optimization]] · Previous: [[Performance, loading and mobile optimization - Loading and streaming]] · Next: [[Memory, rendering and network]]

1. Preload from a LocalScript, targeting the copies in PlayerGui, not StarterGui.
2. Set the Image property before calling ContentProvider:PreloadAsync.
3. Pass the actual ImageLabel or ImageButton instances.
4. Most reliable: keep the menu Visible behind an opaque cover until every image reports IsLoaded.
5. Don't set ScreenGui.Enabled = false while loading.
