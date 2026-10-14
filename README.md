# Roll and Steal an Egg! — dev notes

Notes and history from the Claude Code sessions that built **Roll and Steal an Egg!** (Roblox place 122907972658396).

| Path | What it is |
|---|---|
| [DEV_NOTES.md](DEV_NOTES.md) | Every change made to the game, newest first, plus standing rules and open items. Start here. |
| [handoffs/](handoffs/2026-10-13-first-minute-polish.md) | Handoff briefs for the next session/model (e.g. first-minute polish: HUGE EGG timer, biggest-egg board, onboarding funnel, shorter-feeling walk). |
| [skills/roll-egg-art-direction/](skills/roll-egg-art-direction/SKILL.md) | The art-direction skill (measured Steal an Egg UI/world recipes). Copy into `~/.claude/skills/` to use it. |
| [memory/](memory/MEMORY.md) | Claude's memory notes about how to work on this project (no playtesting, Creator Hub is done by hand, UI lives in StarterGui...). |
| [huds-guide/](huds-guide/Hud's%20Guide%20To%20Roblox%20Success.md) | *Hud's Guide To Roblox Success*: 404 linked notes on growing a Roblox game (analytics, algorithm, ads, thumbnails, retention, monetization, design, Studio tips). Imported from the Google Drive folder "Obsidian Vault - Huds Guide". Notes use `[[wikilinks]]`, so read them in Obsidian. |
| `transcript/` | The full conversation, in three parts (`part1` oldest). Gzipped JSONL, one message per line; embedded screenshots were replaced by `<image removed>`. Unzip with 7-Zip or `gzip -d`. `metadata.json` = session info. |

## Opening in Obsidian

This folder is an Obsidian vault. In Obsidian choose **Open folder as vault** and pick this repo's folder. The `.obsidian/` settings come with it:

- New links are written as relative markdown links, so they still work on GitHub.
- `transcript/` is hidden from search and the graph (it is large, gzipped JSONL).
- README, DEV_NOTES, the memory index and Hud's Guide are bookmarked.
- Graph view colours the guide's notes by chapter.

`huds-guide/` also works as a vault by itself: open just that folder to get the guide alone, with its own graph colours.

Your own layout (`.obsidian/workspace.json`) stays out of git.

Not included: the game itself. Scripts live inside the Roblox place in Studio, not as files here.

This is a snapshot copy: new changes reach this repo only when they're exported and committed again.
