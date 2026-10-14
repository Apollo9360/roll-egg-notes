---
tags: [huds-guide, technical]
---

# Animation, VFX, audio and video

Up: [[Technical and Studio Tips]] · Previous: [[Models, meshes, textures and building]] · Next: [[Security, exploits, free models and plugins]]

- Rig and animate pets in the Roblox animator or Blender; Mesh2Motion auto-rigs. Flying pets are easier than 4-leg walks.
- Tool hold pose: adjust Tool Grip properties or use an animation.
- Bones missing in the Animation Editor: connect them with Motor6Ds.
- Ragdoll is scripted, not animated; add a recovery animation.
- Sync VFX with animation markers via AnimationTrack:GetMarkerReachedSignal("name"). Markers only time it; the VFX must be scripted.
- Don't vibe-code VFX: have AI apply bought or free VFX packs, and hire an artist if effects are core.
- The VFX Studio plugin is popular, but its custom textures may need approval to show live.
- Frequent VFX such as grass-cutting effects can cause random frame stutters; pool and cap them.
- Sound effects (SFX): search the Toolbox or Creator Store creatively. AI picks bad sounds because it can't hear them.
- Lifting sounds from indie games is a copyright risk. Make your own or find CC0 (public-domain) sources.
- In-game short-video feeds use uploaded Roblox videos at ~2,000 Robux each. One popular game reportedly spent over 1M Robux on uploads.
- To save on video uploads, upload a few long (~5-minute) compilations and seek to random timestamps.

---
Related concepts: [[AI Tools]]
