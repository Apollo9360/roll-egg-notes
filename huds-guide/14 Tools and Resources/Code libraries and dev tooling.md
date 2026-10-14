---
tags: [huds-guide, tools]
---

# Code libraries and dev tooling

Up: [[Tools and Resources]] · Previous: [[Studio plugins]] · Next: [[UI kits, icons, fonts and design]]

| Tool | What it's for | Community verdict | Confidence |
|---|---|---|---|
| [ProfileStore](https://devforum.roblox.com/t/profilestore-save-your-player-data-easy-datastore-module/3190543) plus Replica | Player data saving and client replication | Use instead of the old ProfileService. Hand-rolled DataStores tend to lose data past about 100 CCU. Strict type-check warnings are safe to ignore. | High |
| [wally.run](https://wally.run) | Package manager | Get frameworks here or from DevForum, not YouTube. | Medium |
| Signal, Janitor, Promise v4 | Core utilities | Recommended set. | Medium |
| Knit, [Flamework](https://flamework.fireboltofdeath.dev/) | Game frameworks | Knit is fine for simulators and tycoons. Flamework has an [RDC talk](https://www.youtube.com/watch?v=OHqMLEL5QnY). | Low |
| React Luau, Vide, [Rex](https://devforum.roblox.com/t/rex-modern-reactive-ui-framework-for-roblox/3778193) | Declarative UI with state | AIs have Vide docs indexed. | Medium |
| [TopbarPlus](https://devforum.roblox.com/t/topbarplus-v340-construct-topbar-icons-with-ease-customise-them-with-themes-dropdowns-captions-labels-and-more/1017485) | Custom topbar buttons | Recreates Roblox default-style topbar buttons. Shared often in both channels. | High |
| [Satchel](https://devforum.roblox.com/t/satchel-open-source-modern-backpack-system/2451549) | Open-source modern backpack | Shared repeatedly. | Medium |
| [ExpressivePrompts](https://devforum.roblox.com/t/expressiveprompts-customizable-proximityprompt-ui-with-animation-sound-and-more/4513598), [custom prompts](https://devforum.roblox.com/t/create-custom-proximity-prompts-a-bit-easier/3977955), [prompt customizer](https://devforum.roblox.com/t/proximity-prompt-customizer/1663458) | Custom ProximityPrompt UI | ExpressivePrompts adds animation and sound. | Medium |
| [SpotlightUI](https://devforum.roblox.com/t/spotlightui-guided-focus-module/4267581), onboarding highlight modules | Guided-focus tutorials; highlighting objects and UI | Useful for onboarding; the highlight modules are free from a tutorial video. | Medium |
| InfiniteMath, BigNum, [BigInteger](https://devforum.roblox.com/t/201-biginteger-safely-store-and-represent-values-over-2%E2%81%B5%C2%B3/587199) | Big-number math for simulators | Standard options. | Medium |
| Rojo, Argon, Azul | Sync code to the filesystem and GitHub | Rojo supports two-way sync. VS Code is overkill for most beginners. Invisible characters from Rojo or Codex can cause orange underlines. | High |
| [Luau LSP](https://devforum.roblox.com/t/luau-language-server-for-external-editors/2185389), selene, StyLua | Language server, linter, formatter for external editors | Standard external-editor set. | Medium |
| RoProxy | Calls Roblox web APIs from a game | E.g. verifying likes for like rewards. | Medium |
| MicroProfiler, Script Profiler, [Scene Analysis beta](https://devforum.roblox.com/t/studio-beta-scene-analysis-tool/4620678) | Performance diagnostics | Ctrl+F6 opens MicroProfiler. Use these instead of asking AI to "optimize". | Medium |
| [Extended Services for compute](https://devforum.roblox.com/t/scale-your-game-performance-with-extended-services-for-compute/4665165), [EditableMesh batching beta](https://devforum.roblox.com/t/studio-beta-editablemesh-batching-apis-parallel-queries/4779401) | Scaling server compute; mesh performance | Official betas. | Medium |

---
Related concepts: [[Onboarding and Tutorials]] · [[Icons and Titles]] · [[CCU (Concurrent Users)]] · [[AI Tools]]
