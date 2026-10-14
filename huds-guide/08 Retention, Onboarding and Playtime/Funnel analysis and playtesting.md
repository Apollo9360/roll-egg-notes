---
tags: [huds-guide, retention]
---

# Funnel analysis and playtesting

Up: [[Retention, Onboarding and Playtime]] · Previous: [[Contextual steps and progressive reveal]] · Next: [[Bounce rate and first-session fixes]]

1. Add funnel events for every onboarding step and track everything.
2. Find the 3 biggest drop-offs.
3. Split funnels by device and check the phone experience first.
4. Watch real new players live and hotfix, rather than waiting for data.
5. Make one change, wait a few days, and re-read the funnel.

| Funnel symptom | Typical cause | Fix | Confidence |
|---|---|---|---|
| Large drop at one specific step | Missing input support, such as no console keybinds or timeout; console share can exceed 50% | Test on every input device | Medium |
| Drop on the first task | Task takes several repetitions, such as 2 trips to fill a starter ship | Make it completable in one go | Medium |
| Drop at "close this UI" or first purchase steps | Friction steps, such as a backpack players cannot close | Simplify or remove them | High |
| About 25% leave at an action step | Instruction never states the action, such as "click" | State the action explicitly | Medium |
| Stall at an early goal | Requirement too hard, such as a 20-minute hatch or 200 items | Make early requirements quickly achievable | High |
| Step at 80% followed by one at 40% | A specific broken or confusing step | Investigate that step first | Medium |
| Game stuck at 10–20 CCU (concurrent users) | Broken tutorial for new players | Retest the new-player flow | High |

- The second tutorial step usually has the most impact on onboarding.
- Errors on onboarding steps make many people leave; one bug held completion at 30%, rising to 80% after the fix.
- That fix came too late to save the launch, so test the tutorial before buying ads.
- Nothing should go wrong in a tutorial, such as being killed by another player.
- Removing a heavy prerequisite step took one funnel from 16% to 80% conversion.
- Improving overall visuals and game feel raised one tutorial's completion from 45% to 75% without touching the tutorial.
- Trade-off: an easier tutorial raised completion from 50% to 70% but lowered average playtime.

### Tracking and A/B testing

- Log every tutorial step to a funnel to see exactly where players quit.
- Before official stats arrive, custom logging of tutorial stage, such as in DataStores, fills the gap.
- A/B test with and without a tutorial; it significantly affects analytics.
- One A/B winner reached 70% completion with 31% of players staying 12+ minutes.
- In one experiment, a tutorial woven into gameplay gave 6.8 minutes average playtime versus 4.8 for a standard tutorial.
- Add an exit feedback box, such as "this game wasn't what I expected", to separate expectation mismatch from tutorial problems.
- Iterate fast at launch; some teams ship 7–10 onboarding iterations a day and patch daily.
- The owner redid one game's tutorial 3 times before it even reached home recommendations.

### Playtesting

- Play your own game as a new player; tiny frictions such as a missing prompt make players leave immediately (owner-recommended).
- Have a young relative play silently while you watch, or pay a random kid about 50 Robux; use a new tester each time.
- Add an admin panel that resets your own data, since new-player-only tutorials can silently break.
- Play with your brain "turned off", as kids do; the developer's 3 minutes is about 6 minutes for a new player.
- Get feedback from average players who leave, not dedicated testers.
- Act only when many people give the same feedback; log it in a doc and act on trends.

---
Related concepts: [[Onboarding and Tutorials]] · [[Funnels]] · [[Playtime]] · [[Home Recommendations]] · [[CCU (Concurrent Users)]] · [[Updates and Live Ops]]
