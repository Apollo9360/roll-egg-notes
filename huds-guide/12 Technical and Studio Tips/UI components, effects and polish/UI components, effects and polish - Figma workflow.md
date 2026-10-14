---
tags: [huds-guide, technical]
---

# UI components, effects and polish - Figma workflow

Up: [[UI components, effects and polish]] · Previous: [[Declarative UI and tooling]] · Next: [[UI components, effects and polish - BillboardGui and SurfaceGui]]

- Design at 16:9 for PC, export so the image fills the export frame, then scale with UIScale.
- Upload each PNG manually as a decal sized to the closest Figma size, with ScaleType Fit to avoid low quality.
- Exporting at 1000x1000 and importing at 500x500, or matching 500x500 Figma frames to Studio frames, keeps images sharp.
- Figma-to-Roblox plugins: [FigBloxUI](https://devforum.roblox.com/t/figbloxui-%E2%80%94-figma-to-roblox-ui-in-seconds/4446977) (preferred), [Bloxporter](https://devforum.roblox.com/t/bloxporter-beta-paid-fast-figma-roblox-with-custom-configurations/3501193) (paid) and [Figma to Roblox](https://www.figma.com/community/plugin/1221497650571322872/figma-to-roblox).
- Inner strokes and overlay blending don't translate well into Studio.
- Alternatively, have an AI agent write a build script per screen; about 3 minutes each was reported.
- The Figma connector for Claude works, but the free Figma plan caps changes per session.

---
Related concepts: [[AI Tools]]
