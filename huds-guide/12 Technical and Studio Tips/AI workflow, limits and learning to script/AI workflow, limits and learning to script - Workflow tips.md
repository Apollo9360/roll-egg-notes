---
tags: [huds-guide, technical]
---

# AI workflow, limits and learning to script - Workflow tips

Up: [[AI workflow, limits and learning to script]] · Next: [[What AI is weak at]]

- Start a new chat or session per task; long chats burn tokens. Have the agent log changes to a README.
- Use Projects, memory and skills: skill .md files, an .md UI style guide, and an animations.md for conventions.
- Compiling skills from GitHub repos plus uncopylocked games and templates gives consistent builds.
- Add a "Verification" section to agents.md so agents skip playtests and screenshots on static changes. Reserve focused tests for purchases, saving, physics and UI lifecycle.
- Describe the end result, not where scripts go. Ask a cheaper model to explain what it did.
- Give detailed prompts with exact asset IDs, colors and UI hierarchy paths. Vague prompts make the AI rewrite everything.
- Give GPT models 1–4 tasks at a time and put anything hard in its own prompt. Watch for it changing test conditions.
- Describe the game loop semi-in-depth and compare it to an existing game.
- Experienced developers specify structure, performance and security and let AI fill in scripts; pure vibe-coding degrades over time.
- Give the AI free asset packs, icon packs and templates; templates beat images.
- For UI, give 1920x1080 full-screen screenshots of other games' UI as reference.
- Transcribe YouTube videos and podcasts on game design, and build prompts from them (owner-recommended).
- Have Claude simulate progression speed, then tune with player feedback. Simulating ~100,000 sessions flags balancing flaws in seconds.
- Keep the place open while the agent edits it live; don't ask a chat app to regenerate a whole place file per update.

---
Related concepts: [[AI Tools]] · [[Icons and Titles]] · [[Core Loop and Progression]] · [[Updates and Live Ops]]
