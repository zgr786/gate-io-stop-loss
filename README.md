# gate io stop loss: how to set it on spot and futures, fix a stop that never triggered, and what the exit costs

Search this term and you land in a pile of pages that quietly mix three different things: the TP/SL you attach to a spot limit order, the standalone stop-limit order type in the spot panel, and futures conditional orders. On Gate these are separate tools with separate rules, and one of them behaves in a way that surprises almost everyone the first time.

So the useful question isn't "where is the stop loss button". It's which of the three you actually need, what happens after you place it, and what your exit really costs.

## First, pick your market — the tools are not the same

Gate splits stop-loss handling by product:

- **Spot**: stop loss lives inside order placement. You either attach TP/SL to a limit order, or you use the stop-limit order type where the trigger price and the order price are two separate fields.
- **Futures and perpetuals**: stop loss is a position-level feature with five distinct types, including one based on maintenance margin rate.

If your stop "didn't work" on spot, the odds are decent you're comparing it to how futures stops behave. They don't work the same way.

## Setting a stop loss on Gate spot

The attached version, which is the one most people mean:

1. Open the spot trading page and pick your pair.
2. Choose **Limit** as the order type and enter your price and amount.
3. Tick the **TP/SL** checkbox below the order form. A new section appears.
4. Set your take-profit and stop-loss as a percentage move away from your limit price, or click **Advanced** to enter exact trigger prices and switch to a limit order for the exit.
5. Submit.

Here's the part worth reading twice: **none of it is armed until your limit order fills completely.** If your buy order only fills halfway because the price moved off your level, the TP/SL orders don't exist yet — the market can hit your stop level and nothing happens. Gate arms the protection using the quantity that actually filled.

Once the limit order fills, Gate creates two orders: one limit take-profit and one limit stop-loss. The first one to trigger and fill causes the system to cancel the other automatically. You can edit both while the original limit order is still unfilled. After it fills, they become cancel-only.

There's a second trap in the minimum order size. The stop-loss leg can occasionally fail to place at all because its notional value falls under Gate's minimum, leaving you with a take-profit order and no protection underneath it. If only one of the two appears in your open orders, that's usually why.

The other mechanism is the **stop-limit order type** in the order panel dropdown, where the fields are laid out differently:

- **Trigger price** — the level that activates the order. Doesn't have to equal your fill price.
- **Order price** — what actually gets sent to the book once triggered.
- **Quantity** — how much goes out.

Gate's own order-type documentation is blunt about this: the trigger price decides when your order goes live, not what you get filled at.

If you haven't got an account open yet, 👉 [👉 Create a free Gate account and start from the spot order panel](https://bit.ly/GateVIP) — the checkbox is easier to find once you're looking at it.

## Stop loss on Gate futures: five types, and you'll probably use two

Gate's futures TP/SL menu offers:

| Type | What it does | Realistic use |
| --- | --- | --- |
| Order TP/SL | Attached when you open a position; placed automatically once the entry order fills | The default. Set it once, forget it |
| Entire position TP/SL | Closes the whole position on trigger | Clean single-level exits |
| Partial position TP/SL | Closes a set amount of an existing position | Scaling out in pieces |
| Trailing TP | Moves the trigger price as the market moves in your favour | Trend trades where you don't want a fixed target |
| MMR stop-loss | Triggers on maintenance margin rate rather than price | Account-level risk control |

The setup flow:

**On a new position** — go to the futures page, pick your pair, choose Market or Limit, click **TP/SL** to expand the panel, pick Buy/Long or Sell/Short, enter your trigger prices (for a long, take-profit above entry and stop-loss below it), select the trigger price type, enter your size, submit.

**On a position you already hold** — two routes. Either find the **TP/SL** column in your Position tab and hit the edit icon, or go to Open Orders, find the TP/SL column and click **Add**. You can then set conditions by price or by return percentage.

Two details that matter more than the clicks:

Futures TP/SL orders do **not** lock margin until they trigger. That means your account can look fine today and produce a failed stop tomorrow if you've moved the available balance elsewhere. Check your available balance before you rely on a stop you set last week.

And every futures TP/SL order is flagged **reduce-only**, so it closes exposure rather than flipping you into the opposite direction. Manually closing a position cancels all the TP/SL orders attached to that market.

## Last price, mark price, index price — the setting that decides whether your stop fires

This is where most "my stop didn't trigger" complaints come from.

Gate lets you choose which price feeds the trigger:

- **Last price** — the most recent traded price. Fine in normal conditions, noisy in thin markets.
- **Mark price** — a calculated reference price designed to be resistant to manipulation. Better for volatile or low-liquidity pairs.
- **Index price** — an average pulled from multiple exchanges. Used for cross-exchange strategies.

These three numbers are not the same at any given moment, especially during a fast move. If you set a stop off last price and you're watching the mark price on your chart, you can sit there convinced your level was hit while the contract that actually governs your order hasn't touched it. Pick one and watch that one.

## Stop market vs stop limit: choose your failure mode

Neither version is "better". They fail differently.

|  | Stop market | Stop limit |
| --- | --- | --- |
| Execution | Matches at the best available price | Only at your limit or better |
| What's guaranteed | Getting out | Getting your price |
| What isn't | Your fill price | That you get out at all |
| Slippage risk | Higher | Lower |
| Non-fill risk | Low | Real, if price gaps past your limit |
| Suits | Fast, liquid markets | Thin markets, larger positions |

The short version: a stop market gets you out and may hand you a worse price. A stop limit protects the price and may leave you holding a losing position when the market runs straight through your limit without filling it. Gate's own docs note the average fill price on a triggered market exit can differ from the last price on your chart once the order has to match across several levels of the book.

## Why your Gate stop loss didn't trigger or filled somewhere else

Going through the documented causes, roughly in order of how often they bite:

- **Wrong trigger price type.** You watched last price, your order was keyed to mark price.
- **Insufficient available balance.** Futures stops don't reserve margin until they fire.
- **Order below the minimum size.** Common on spot take-profit legs.
- **Cross margin, closing price below bankruptcy price.** The system can't execute a close that would push you past that line.
- **Opposite-direction open orders.** A pending order pointing the other way can open a new position when your TP/SL fires, and the margin check then fails.
- **Extreme volatility or thin depth.** Your trigger hits, the market is already somewhere else.
- **Position size above the platform limit** for TP/SL orders.
- **Stop set too close to the liquidation price.** Gate advises leaving enough distance for the stop to execute before liquidation logic takes over.
- **You closed the position manually.** That wipes every TP/SL order on that market.

> A stop loss doesn't prevent losses. It caps them, and only if it executes. Gate says as much in its own documentation.

If you need to be out immediately and a conditional order isn't behaving, closing manually is a legitimate move. Just know that it cancels the attached orders with it.

## What a stop loss actually costs on Gate

Nothing extra. Gate's documentation states plainly that TP/SL orders carry no additional charge — you pay the standard trading commission. Spot exits placed as limit orders bill the maker rate; market exits and most triggered stops bill the taker rate.

That's where the fee tier matters, because a stop-loss exit is usually a taker order, and taker rates are the expensive side of the table.

Gate's spot fees run across 17 tiers, VIP 0 through VIP 16. Your tier is assigned automatically from your 30-day trading volume, your 14-day average GT holdings, or your account asset value — whichever qualifies you highest. It is not a subscription and there is no monthly fee.

| Tier | 30-day spot volume to qualify | Spot maker | Spot taker | Rate paying fees in GT (maker/taker) | Account |
| --- | --- | --- | --- | --- | --- |
| VIP 0 | 0 | 0.10% | 0.10% | 0.09% / 0.09% | [ Create a free Gate account](https://bit.ly/GateVIP) |
| VIP 1 | $60,000 | 0.099% | 0.099% | 0.089% / 0.089% | [ Sign up and start clearing volume](https://bit.ly/GateVIP) |
| VIP 2 | $120,000 | 0.098% | 0.098% | 0.088% / 0.088% | [ Open an account to work toward this tier](https://bit.ly/GateVIP) |
| VIP 3 | $240,000 | 0.097% | 0.097% | 0.087% / 0.087% | [ Register on Gate](https://bit.ly/GateVIP) |
| VIP 4 | $500,000 | 0.095% | 0.096% | 0.086% / 0.086% | [ Create your Gate account](https://bit.ly/GateVIP) |
| VIP 5 | $1,000,000 | 0.09% | 0.095% | 0.081% / 0.085% | [ Sign up and check your live rate](https://bit.ly/GateVIP) |
| VIP 6 | $3,000,000 | 0.085% | 0.09% | 0.076% / 0.081% | [ Open a Gate account](https://bit.ly/GateVIP) |
| VIP 7 | $8,000,000 | 0.08% | 0.085% | 0.07% / 0.076% | [ Register and track your tier](https://bit.ly/GateVIP) |
| VIP 8 | $20,000,000 | 0.075% | 0.08% | 0.06% / 0.072% | [ Create an account](https://bit.ly/GateVIP) |
| VIP 9 | $50,000,000 | 0.07% | 0.075% | 0.05% / 0.068% | [ Sign up on Gate](https://bit.ly/GateVIP) |
| VIP 10 | $100,000,000 | 0% | 0.058% | — | [ Open an account to aim for VIP 10](https://bit.ly/GateVIP) |
| VIP 11 | $120,000,000 | 0% | 0.045% | — | [ Register on Gate](https://bit.ly/GateVIP) |
| VIP 12 | $240,000,000 | 0% | 0.037% | — | [ Create your account](https://bit.ly/GateVIP) |
| VIP 13 | $440,000,000 | 0% | 0.03% | — | [ Sign up and start trading](https://bit.ly/GateVIP) |
| VIP 14 | $800,000,000 | 0% | 0.025% | — | [ Open a Gate account](https://bit.ly/GateVIP) |
| VIP 15 | $1,600,000,000 | 0% | 0.022% | — | [ Create an account](https://bit.ly/GateVIP) |
| VIP 16 | $3,000,000,000 | 0% | 0.02% | — | [ Register on Gate](https://bit.ly/GateVIP) |

A few things in that table are worth pointing at directly.

**Maker and taker are identical for the first four tiers.** From VIP 0 through VIP 3, resting an order on the book costs exactly the same as crossing the spread, so posting limit orders buys you nothing at that level. The split only opens up at VIP 4.

**The GT-paid rate stops being lower above VIP 9.** Hold enough GT and you get a genuinely cheaper rate early on — 0.09% instead of 0.10% at VIP 0, which is roughly a tenth off. Gate's published table lists that separate lower rate through VIP 9 and stops listing it after that.

**VIP 15 and VIP 16 aren't reachable by normal upgrades.** Gate's fee documentation states that regular VIP levels cannot be upgraded to VIP 15 or VIP 16, and that accounts with API trading volumes of 60% or more, or accounts already at those levels, get moved to senior institutional status.

**Futures fees sit on a separate ladder.** At VIP 0 the USDT-margined perpetual rates are 0.02% maker and 0.05% taker, which is where most stop-loss triggers will bill you if you're trading contracts.

For scale: a $5,000 stop-loss exit on VIP 0 spot at 0.10% taker costs $5 in commission. The trade itself is the cost, not the stop mechanism.

One more note on accuracy — Gate changed its spot and futures fee structure on 9 April 2026 and says rates can be adjusted at any time. The figures above are the ones published on Gate's fee page, but your own live rate is the number that bills you. 👉 [👉 Create a Gate account and check your actual tier and rate](https://bit.ly/GateVIP) rather than planning around a blog table, including this one.

## Mistakes that quietly cost money

**Setting the stop and then moving it.** The stop you move is the stop that doesn't work. If you can't leave the level alone, set it further out and size the position smaller so you don't need to.

**Stops too tight for the pair's normal range.** If a coin regularly swings 5% within an hour, a 3% stop is a donation. Gate's own educational material flags both extremes: too close and it fires constantly, too far and it protects nothing.

**No stop on the spot side because you bought with a limit order and assumed it was covered.** It isn't covered until that limit order fills completely. Partial fills leave you unprotected.

**Forgetting funding on perpetuals.** Your stop-loss exit triggers a taker fee, and if you hold the position for hours you're also paying funding every eight hours. Neither one shows up in the TP/SL panel.

**Setting the stop by gut feel and then reverse-engineering a target.** Gate's wiki suggests placing take-profit near a resistance level and stop-loss just under the nearest support, with a minimum 1:2 risk-to-reward ratio. That's a starting framework, not a rule — but having a number before you enter beats inventing one after.

## FAQ

**Does setting a stop loss cost anything on Gate?**
No separate charge. You pay the standard trading commission on the exit, plus funding if you're holding a perpetual position.

**Why did my stop fill at a worse price than the trigger?**
Because a market-triggered stop matches against the available book. Large orders can cross several price levels, and thin markets make the gap wider.

**Can I use a stop loss on spot without using a limit order?**
Yes — the stop-limit order type in the spot order panel lets you set a trigger price, an order price and a quantity directly. Just remember the trigger isn't the fill price.

**What happens to my take-profit if my stop-loss fills?**
The system cancels it. Same in reverse.

**Can I set a trailing stop on Gate?**
Yes, as a futures TP/SL type. It adjusts the trigger price as the market moves in your favour, which is useful for trend trades and less useful in choppy ranges.
