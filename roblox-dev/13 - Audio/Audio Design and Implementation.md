---
title: Audio Design and Implementation
category: Audio
tags: [audio, sound, audioplayer, audioemitter, soundgroup, music, sfx, licensing]
status: active
source_quality: AUTHORITATIVE (Roblox audio APIs and import rules) + labelled sound-design practice
last_verified: 2026-10-14
aliases: [Sound design, AudioPlayer, AudioEmitter, Wire, SoundGroups, Music system, Sound manager, Audio licensing]
---

# Audio Design and Implementation

## Summary

Roblox has two audio APIs:
- **Legacy `Sound`** objects with `SoundGroup`s and Sound effects. Simple, and still supported.
- The **newer audio graph**: `AudioPlayer` (the source) → `Wire` → effects (`AudioEqualizer`, `AudioCompressor`, `AudioReverb` …) → `AudioEmitter` (a 3D speaker), heard by an `AudioListener` (a virtual microphone) → `AudioDeviceOutput`. More control: routing, mixing, voice, text-to-speech.

Inserting from the Toolbox creates a legacy `Sound`. **Use Creator Store audio** (100,000+ free professionally produced tracks and effects), or audio **you have rights to**.

## Import rules

Official:
- **Rights:** you need the legal rights, and the audio must follow the Community Rules.
- **Format:** a single track in **mp3, ogg, wav or flac**.
- **Size:** under **20 MB** and **7 minutes**, **≤ 48 kHz**, mono, stereo, 3.0 or 5.1.
- **Quota:** **2,000 imports per 30 days** if ID-verified, **100** if not.
- **Processing:** transcoded on import. Invalid or old-tool files can be rejected. Audio then passes moderation and copyright checks.
- **Sharing:** use **asset privacy** to share private audio with other creators or games.

## Implementation

| Need | Legacy Sound approach | New audio approach |
|---|---|---|
| UI clicks, 2D music | `Sound` in SoundService or PlayerGui; `SoundService:PlayLocalSound(sound)` | `AudioPlayer` wired to an `AudioDeviceOutput` |
| 3D positional sound | `Sound` parented to a part or attachment (`RollOffMode`, `RollOffMinDistance`/`MaxDistance`) | `AudioPlayer` → `AudioEmitter` on the part; listener on the camera or character |
| Volume categories | `SoundGroup`s (Master → Music, SFX, UI, Ambience); set `Sound.SoundGroup` | Wire into shared effect or fader chains per category |
| Muffling, reverb, ducking | Sound effects under a Sound or SoundGroup | `AudioEqualizer`, `AudioReverb`, `AudioCompressor` (sidechain ducking) in the wire graph |

### Practical rules

Common practice:
- **Volume hierarchy.** Expose Music, SFX and UI sliders in settings, saved to the player profile. Music sits under SFX in the default mix so feedback stays audible.
- **Variation.** Randomize pitch (±5–10%) and choose from 2–4 samples for repeated sounds such as footsteps, hits and coin pickups, to avoid machine-gun repetition.
- **Concurrency limits.** Cap simultaneous instances per sound type (e.g. max 4 hit sounds). Pool `Sound`/`AudioPlayer` objects for frequent effects.
- **Play feedback sounds on the client** at the moment of input or result. Only the triggering event replicates. Never create replicated server sounds for every action.
- **Music:**
  - one music controller per client;
  - crossfade between tracks by tweening volume over 1–2 s;
  - switch by zone or state (lobby, combat, boss);
  - layered stems (add percussion in combat) via synchronized players.
- **Feedback design:**
  - UI clicks are short and quiet;
  - rewards get a "cha-ching" scaled by value;
  - errors are distinct and soft;
  - rarer rewards get longer, brighter sounds.
- **Memory.** Audio adds up. Load per area rather than all at once ([[Performance and Profiling Guide]]).
- **Thumbnail videos** may only use music from Roblox's catalog. No voice-over, no songs with lyrics ([[Free AI Image Tools and Thumbnail Prompts]] lists the video rules).

## Common mistakes

- Using audio you don't have rights to. It gets moderated and puts the account at risk.
- Every sound at full volume with no grouping, so players can't turn down just the music.
- The same sample repeated without variation.
- Server-created sounds for client-only feedback.
- Long sounds preloaded in the loading screen, which inflates load time.

## Related notes

- [[VFX Production Guide]]
- [[Roblox UI Engineering Guide]]
- [[Performance and Profiling Guide]]

## Sources

- [Audio overview](https://create.roblox.com/docs/audio), [Audio objects](https://create.roblox.com/docs/audio/objects), [Audio effects](https://create.roblox.com/docs/audio/effects), [Audio assets (import requirements)](https://create.roblox.com/docs/audio/assets), [Legacy Sound overview](https://create.roblox.com/docs/sound). AUTHORITATIVE, 2026-10-13.
- Mixing practice (variation, concurrency, ducking, stems): standard game-audio practice. USEFUL.

## Last Verified

2026-10-14
