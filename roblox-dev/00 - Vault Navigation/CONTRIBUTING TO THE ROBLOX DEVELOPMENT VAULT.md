---
title: CONTRIBUTING TO THE ROBLOX DEVELOPMENT VAULT
category: Vault Navigation
tags: [contributing, standards, process]
status: active
source_quality: Vault policy
last_verified: 2026-10-14
aliases: [Contributing, Vault rules, How to add a note]
---

# CONTRIBUTING TO THE ROBLOX DEVELOPMENT VAULT

## Summary

Anyone, person or AI agent, can add to this vault. Follow four rules: **verify, cite, label, link.** Never add knowledge you can't back up.

## Rules

1. **Verify before you write.** Use the official Roblox docs (create.roblox.com, or the `Roblox/creator-docs` GitHub repo when the site is unreachable) first, then official tool repos, then reputable community sources.
2. **Cite every non-obvious claim** in the note's Sources section, with a link, and label its quality:
   - **AUTHORITATIVE**: official Roblox docs or API reference.
   - **HIGH CONFIDENCE**: official tool repos, release notes, Roblox staff posts.
   - **PROJECT-VERIFIED**: tested in this project (say how).
   - **USEFUL / COMMUNITY OBSERVATION**: reputable community sources, Hud's Guide.
   - **UNVERIFIED**: reported but not confirmed. Keep these clearly marked.
3. **Label contradictions** instead of picking one silently. Use a `> [!warning] Documented contradiction` callout and add it to [[Research Backlog]].
4. **No link dumps.** A source is there to back a claim in the note, not as reading material.
5. **Update `last_verified`** whenever you re-check a note against its sources.

## Code

- `--!strict` Luau, following [[Production Luau Coding Standards]].
- Type-check every code block with luau-lsp and Roblox definitions (or Studio Script Analysis) before adding it. Say in the note if code was not run in a live game.
- Server-authoritative by default; validate remotes; clean up connections ([[Code Review Checklist]]).
- Never paste code or assets from games you don't own, including this project's parked reference decompile.

## Note format

- File name = title. Put it in the numbered folder for its area. Decision guides start with `Decision - `, recipes with `Recipe - `.
- Frontmatter: `title`, `category`, `tags`, `status` (active / draft / deprecated), `source_quality`, `last_verified` (YYYY-MM-DD), `aliases`.
- Sections: Summary, core content, code (if any), Common mistakes, Related notes, Sources, Last Verified.
- Link with `[[wikilinks]]`. Every link must resolve to a note or an alias.
- Write plainly: short sentences, lists for steps and options, tables for comparisons.

## After a change

- Add a line to [[VAULT CHANGELOG]] for new notes, renames, removals or corrected facts.
- Move answered questions out of [[Research Backlog]].
- Project-specific decisions (this game's numbers and systems) go in `DEV_NOTES.md`, not here. Reusable lessons go here.
- No personal memory files in the repo; the vault is the shared memory.

## Last Verified

2026-10-14
