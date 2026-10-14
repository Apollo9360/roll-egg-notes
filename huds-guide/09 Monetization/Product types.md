---
tags: [huds-guide, monetization]
---

# Product types

Up: [[Monetization]] · Previous: [[Core metrics payer conversion vs ARPPU]] · Next: [[Price ladder and anchoring]]

| Type | What it is | Notes | Confidence |
|---|---|---|---|
| Gamepass | One-time permanent purchase | Shows in the game-page Store tab, which cannot be disabled. The game owner automatically owns all of them. | High |
| Developer product | Repeatable purchase such as cash, boosts, revives, skips | Hidden from the Store tab. Only fires a purchase event, so you save ownership yourself. Required for gifting. | High |
| Subscription | Recurring VIP-style purchase | Rarely used; mainly roleplay and niche community games. One-time Robux purchases are said to beat it. | Low |
| Roblox Shop | Roblox's personalized in-game storefront | See the [DevForum announcement](https://devforum.roblox.com/t/introducing-shop-a-personalized-in-game-storefront/4701505/1). | Medium |

- Developer products make up most revenue in most genres. Cheap ones are often cited as about 70% of income.
- Many developers sell almost no gamepasses. Whether gamepasses are dead is disputed.
    - Counterpoint: gamepasses feel premium and keep payers returning.
    - Robux income arrives in bursts (gift cards), so offer both types.
    - Simulator players often prefer gamepasses for 2x boosts, auto-collect and auto-click.
- Aim for more developer products than gamepasses, with roughly 5–8 gamepasses. Some games run 20–60+ developer products.
- Forty developer products are easier to manage and re-price than forty gamepasses.
- Gifting needs developer products. Big games keep a gamepass plus a developer-product twin for gifting, checking ownership in a DataStore first.
- Make every product giftable. Make the Gift button as large as Buy.
- A starter pack works better as a developer product, so it does not appear in the game-page store.
- Swap a 199-Robux permanent 2x gamepass for a 49-Robux 2x-for-20-minutes developer product, or offer both.
- Owner's base-game stack (owner-recommended): spam consumables, a 2x offline-earnings developer product, a shop restock, a 24-hour base lock, and expensive whale gamepasses.
- Add more consumable developer products to raise the number of spending days (owner-recommended).
- New developer products give a short PCR boost, e.g. 0.45% to 1%.
- When adding perks, add them to the existing VIP pass so current owners feel valued.
- Must-haves for most simulator and incremental games: a 2x earnings product plus 2x and 5x offline-earnings developer products.
- A most robux board is almost always worth adding. Build it with a SurfaceGui on a part, setting Adornee and Face.
- Monetization is largely the same across games of one type. Copy small developer products from big games in your genre.
- Gamepass purchases cannot be refunded. Unexpected purchases may indicate a hacked account, so change the password.
- Refunding a duplicate gamepass purchase is fine; it is not treated as selling Robux.
- Refresh gamepass ownership in-game after purchase; forcing a rejoin loses goodwill.
- Show live prices with MarketplaceService:GetProductInfo. A label showing 139 while charging 160 kills sales.
- Buying your own gamepass in Studio can charge real Robux, per a [reported bug](https://devforum.roblox.com/t/bought-gamepass-in-studio-and-still-got-charged-on-roblox/4749186) (low confidence).
- A premium currency adds less purchase friction than direct Robux prices. A Robux-pegged 1:1 currency fits only trading or social games.
- Custom currency names instead of Coins, Gems or Cash are usually fine, depending on demographic.
- Creator Store assets can sell for real money. Sales may take about 2 months to start, then grow without advertising.

---
Related concepts: [[Gamepasses and Dev Products]] · [[Genres and Saturation]] · [[Payer Conversion]] · [[Audience Region]] · [[Updates and Live Ops]]
