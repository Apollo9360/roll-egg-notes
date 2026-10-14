---
tags: [huds-guide, technical]
---

# Studio setup, debugging and publishing - Pre-release checklist

Up: [[Studio setup, debugging and publishing]] · Previous: [[Publishing and live-game gotchas]]

1. Check for memory leaks with memory trending over idle time.
2. Profile script-heavy interactions with the MicroProfiler.
3. Remove free-model scripts from Workspace; backdoors can run server code and wreck DataStores.
4. Add a funnel event for every tutorial step.
5. Test in the live Roblox client, since some systems and UI labels behave differently than in Studio.
6. Confirm nothing trusts the client, then play the whole game yourself.

---
Related concepts: [[Onboarding and Tutorials]] · [[Funnels]] · [[Updates and Live Ops]]
