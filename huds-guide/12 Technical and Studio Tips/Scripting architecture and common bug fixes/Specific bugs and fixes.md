---
tags: [huds-guide, technical]
---

# Specific bugs and fixes

Up: [[Scripting architecture and common bug fixes]] · Previous: [[Scripting architecture and common bug fixes - Architecture]] · Next: [[Scripting architecture and common bug fixes - Command-bar snippets]]

| Symptom | Fix | Confidence |
|---|---|---|
| Tools sharing one script don't work | Disable Tool.RequiresHandle (on by default); clean up on tool.Destroying to avoid leaks | Medium |
| Touched handler can't find the player | The argument is the other part; use hit.Parent and Players:GetPlayerFromCharacter() | High |
| Data keyed by username breaks | Store by UserId, since usernames change | High |
| Walk sound stops after respawn | Connect to the existing character and CharacterAdded; recreate the sound; use Humanoid.Running or MoveDirection.Magnitude | Medium |
| WalkSpeed changed signal never fires | GetPropertyChangedSignal only fires on change, not on movement | High |
| Players frozen after a boss fight or cutscene | WalkSpeed was never restored | Low |
| Game invite prompt | SocialService:CanSendGameInviteAsync(player), then SocialService:PromptGameInvite(player) | High |
| Purchase fails with 404 or "account not charged" | Check you call the gamepass prompt vs the dev-product prompt correctly | Medium |
| ViewportFrame shows nothing | Camera sits at 0,0,0 inside the model; move it out | High |
| Sounds heard map-wide | Set the rolloff range | High |
| Animation won't play | Check ownership: a group game needs a group-owned animation | High |
| Idle overrides a slash so only the handle swings | Fix animation priority | High |
| Players can't pass through a union doorway | Set CollisionFidelity to PreciseConvexDecomposition | Medium |
| Players stuck inside props like an oven | Add an invisible blocking part around the prop | Low |
| Particle works in Studio but not live | Likely an unapproved custom texture (e.g. from the VFX Studio plugin) | Medium |
| Daily reward can't be collected | Reward item exceeded a capacity limit such as a full bag; check rewards against limits | Low |
| Mobile player opens the leave menu | Detect with GuiService.MenuOpened | Medium |
| Mobile hold-to-throw fights camera drag | Add a GUI throw button | Medium |
| Mobile hover fires while finger is held | Handle clicks on release | Medium |

- Pickup or area detection: use WorldRoot:GetPartsInPart or workspace:GetPartBoundsInRadius in a module on a client tick. It beats looping magnitude checks.
- IntValue caps at ~9.22 quintillion (int64). NumberValue (double) goes higher but loses precision above 2^53.
- Incremental games need a big-number library such as InfiniteMath, a DevForum BigNum module or [BigInteger](https://devforum.roblox.com/t/201-biginteger-safely-store-and-represent-values-over-2%E2%81%B5%C2%B3/587199).
- Random number generation (RNG) systems: roll server-side with the Random class and a weighted chance table ([DevForum weighted random](https://devforum.roblox.com/t/weighted-random/1762512)). Layer pity, luck boosts and auto-roll on top.
- Custom ProximityPrompt visuals: set Style to Custom and script your own UI. Ready-made options include [custom prompts](https://devforum.roblox.com/t/create-custom-proximity-prompts-a-bit-easier/3977955) and the [Proximity Prompt Customizer](https://devforum.roblox.com/t/proximity-prompt-customizer/1663458).
- R15 body scaling uses Humanoid NumberValues BodyWidthScale, BodyDepthScale, BodyHeightScale and HeadScale. R6 doesn't support this.
- Free-form text input (e.g. naming a farm) is user-generated content and must pass Roblox's TextService filter before others see it.

---
Related concepts: [[Gamepasses and Dev Products]]
