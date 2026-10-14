---
tags: [huds-guide, monetization]
---

# Terms of Service limits

Up: [[Monetization]] · Previous: [[Other income Creator Rewards and ads]] · Next: [[Payouts, Creator Rewards and DevEx]]

- Robux gambling is against the Terms of Service (ToS): Plinko, mines, buying spins with Robux, or Robux prizes. Games have been banned for adding Plinko.
- Paid random items rule: there can be no outcome where the player gets nothing for the payment.
    - Violation example: a spin wheel giving +XP to a max-level player.
    - Fix: give max-level players a different wheel or rewards, or lock the wheel for them.
- Show odds for paid random items. Items from paid loot boxes must not be tradeable for Robux, USD or limiteds.
- Under-18 players in the UK and Australia cannot buy paid random items (rolls, crates, eggs, lucky blocks) with Robux.
    - Answer "yes" to the paid random item question in the maturity questionnaire.
    - Script a PolicyService check and hide the purchase for ineligible players.
    - Give them a free-currency or playtime route instead.
- Belgium and the Netherlands also restrict paid random items. Skipping the check risks a permanent per-country ban with no appeal.
- Showing all odds does not remove the regional-policy check. Telling players to use a VPN is not a fix.
- An RNG pack mechanic does not block all-ages reach.
- False advertising is not allowed: a "2x" that gives 1.2x or 1.5x violates it. Inflated "4096x" labels carry the same risk.
- Gambling or casino simulators may be allowed only with zero monetization (disputed). Such games have historically been banned.

### Open policy questions

- Robux-boosted roll odds in countries with strict gambling laws.
- Paid seeds without displayed odds in grow-style games.
- Whether a paid restock counts as a paid random item.
- Whether luck boosts or server luck need displayed odds.
- Whether refunding 50% on a duplicate counts as a benefit.
- Whether paid keys that speed up a timed random chest need displayed odds.
- Whether coin boosters (coins buy spins or crates) count as paid random items in restricted regions.
- Whether a mystery box must show drop rates.
- Whether a guaranteed 1% drop within 1–5 spins works as a hook.

---
Related concepts: [[Moderation and Bans]] · [[Maturity Questionnaire]] · [[Playtime]] · [[Age Gate and All-Ages Reach]] · [[Regional Pricing]]
