# Roll and Steal an Egg! — dev notes

Notes and history from the Claude Code sessions that built **Roll and Steal an Egg!** (Roblox place 122907972658396).

| Path | What it is |
|---|---|
| `DEV_NOTES.md` | Every change made to the game, newest first, plus standing rules and open items. Start here. |
| `handoffs/` | Handoff briefs for the next session/model (e.g. first-minute polish: HUGE EGG timer, biggest-egg board, onboarding funnel, shorter-feeling walk). |
| `skills/roll-egg-art-direction/` | The art-direction skill (measured Steal an Egg UI/world recipes). Copy into `~/.claude/skills/` to use it. |
| `memory/` | Claude's memory notes about how to work on this project (no playtesting, Creator Hub is done by hand, UI lives in StarterGui...). |
| `transcript/` | The full conversation, in three parts (`part1` oldest). Gzipped JSONL, one message per line; embedded screenshots were replaced by `<image removed>`. Unzip with 7-Zip or `gzip -d`. `metadata.json` = session info. |

Not included: the game itself. Scripts live inside the Roblox place in Studio, not as files here.

This is a snapshot copy: new changes reach this repo only when they're exported and committed again.
