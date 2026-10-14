---
tags: [huds-guide, technical]
---

# Publishing and live-game gotchas

Up: [[Studio setup, debugging and publishing]] · Previous: [[Studio bugs and fixes]] · Next: [[Studio setup, debugging and publishing - Pre-release checklist]]

- "Saved to Roblox" is not published; you must Publish.
- Never privatize a live game to fix a bug (one fell from 30 algorithm CCU to 5). Publish the fix and restart servers.
- Use a soft-shutdown script that rejoins players. Games at 1k and 2k CCU lost momentum after repeated hard restarts.
- Emergency fix for a broken tool: set its Enabled to false, republish immediately, then debug calmly.
- If published changes don't appear, log out of Studio and back in. For stale UI, close Studio, roll back, then reopen.
- Keep a separate test place with separate DataStores for closed testing.
- Uploading a dev version over the main place does not wipe DataStores.
- To reset data before full release, change the DataStore key (e.g. PlayerData_Test_v1). No new place is needed.
- Republishing as a new place forces you to recreate every gamepass and developer product ID. A game with ~40 visits doesn't need it before ads.
- A "Needs attention" flag was once traced to the unpaid 1,000 Robux publishing fee.
- All-ages reach currently needs 250 highly engaged players (HEP) plus that 1,000 Robux fee or Roblox Plus. A 50,000 Robux expedited review skips the HEP requirement.
- You can restrict device types (e.g. console) at release and add them later; console support is always worth adding.
- Platform-wide DataStore outages happen. Don't panic-push data changes during one.
- HTTP request rate limits have changed without notice ([DevForum thread](https://devforum.roblox.com/t/http-request-ratelimit-changed-wo-notice/4721029)).
- Uncopylocking a game invites instant clones; one 80k-visit game spawned about two dozen copies of its title, icon and thumbnail.

---
Related concepts: [[HEP (Highly Engaged Players)]] · [[Expedite Fee]] · [[Icons and Titles]] · [[CCU (Concurrent Users)]] · [[Gamepasses and Dev Products]] · [[Age Gate and All-Ages Reach]] · [[Thumbnails]] · [[Genres and Saturation]]
