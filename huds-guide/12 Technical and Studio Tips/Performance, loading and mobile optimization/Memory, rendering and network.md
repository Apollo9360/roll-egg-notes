---
tags: [huds-guide, technical]
---

# Memory, rendering and network

Up: [[Performance, loading and mobile optimization]] · Previous: [[Performance, loading and mobile optimization - Late-loading UI images]] · Next: [[Mobile and network feel]]

- Memory trending upward over time is a leak; flat memory isn't. Check with an instance-counter plugin while idle.
- Leaks also show up as a high crash rate.
- Textures have draw-call costs. Clearing ~20,000 textures raised FPS from ~40 to ~200 in one stud-style game.
- Prefer materials over textures on big maps, and remove hidden backfaces in stud-style builds.
- Oversized textures alone can consume ~2 GB. Removing shadows via plugin saved 2 GB on a big map.
- Unoptimized models and part counts hurt more than background scripts.
- Use low-tri models for big maps, and check the triangle count of what the player sees.
- Disable VFX when the player isn't near another base or their own.
- Billboards and SurfaceGuis with infinite MaxDistance all render at once. Set MaxDistance to ~100.
- Disable RichText where it isn't needed.
- Pause UI animations while their parent frame is hidden.
- Constant replication (e.g. a hover module on RunService) burns bandwidth. Use UnreliableRemoteEvents and interpolate on the client.
- Packing data with buffers cuts memory and bandwidth; one experienced developer tied it to better playtime on large games.
- Review AI code for runaway loops; one AI script updated 244 parts 14,000 times a second.
- Bullets: pool template clones instead of Instance.new and Destroy, throttle rotation updates, and use an entity component system (ECS) at scale.
- Mass-spawning models causes lag spikes; investigate with the MicroProfiler.
- For weather, use a raycast-based system such as [high-performance rain](https://devforum.roblox.com/t/high-performance-raycast-rain/3621951).

---
Related concepts: [[Updates and Live Ops]] · [[Test Bursts and Spikes]] · [[Moderation and Bans]] · [[AI Tools]] · [[Playtime]] · [[Genres and Saturation]]
