---
type: strategy
description: The Turtle Trading System (Dennis/Eckhardt, 1983) — the systematic-momentum corner of the four-sleeve odyssey (Turtles Book U20808939). Donchian-channel breakout entries, N-based (ATR) position sizing, pyramiding, 2N stops, and correlation-capped unit limits. Public, mechanical, backtestable — which is exactly why it's here.
tags: [trading, strategy, momentum, trend, turtles, systematic]
status: living
created: 2026-07-21
---
## How to implement turtle trades

1. 55 day donchian high
2. 20 day donchian low must be going up like a staircase
3. ETF only
4. Enter only close to 20ema
5. 20 day donchian low no more than 5% away from entry
6. Avoid ETF that have widening 55/20 donchian levels


# Turtle trader system

The **momentum · systematic · longer-hold** corner of the [[multi strategy fund|four-sleeve
odyssey]] (Turtles Book U20808939). Where [[Momentum discretion]] ([[martin luk]]) teaches
momentum as discretionary *feel*, this sleeve teaches momentum as a **fully mechanical
rules-engine** — the opposite temperament, deliberately. Its whole value in the curriculum is
that every rule is public, defined, and backtestable, so compliance can be scored exactly.

Originated by **Richard Dennis and William Eckhardt** (1983 "Turtles" experiment) to prove
trading could be taught as a system. The rules below are the classic public ruleset (Faith,
*Way of the Turtle*; Covel, *The Complete TurtleTrader*).

## The two systems

- **System 1 (short-term):** enter on a **20-day** Donchian breakout; exit on a **10-day**
  opposite breakout. Filter: *skip* the entry if the last 20-day breakout would have been a
  winner (prevents over-trading the same trend).
- **System 2 (long-term):** enter on a **55-day** Donchian breakout; exit on a **20-day**
  opposite breakout. **Always taken** — no filter.

This book runs a **System-2-style 55/20**: 55-day breakout entry, 20-day lower-channel exit.
See [[total trading system v1 runs turtle exits without turtle sizing]] for the live audit of
where the current book diverges from these rules.

## N (volatility unit) and position sizing — the core the current book is missing

- **N = 20-day ATR** (Wilder's average true range). N is the market's volatility in price terms.
- **Dollar volatility per unit = N × dollars-per-point** of the instrument.
- **1 Unit = 1% of account equity ÷ dollar-volatility.** Every position is sized so that a 1N
  move ≈ 1% of the account. This is what makes a volatile and a quiet market carry *equal* risk —
  flat dollar sizing (what the current book does) is exactly the error N-sizing was invented to
  prevent.
- Recompute N and unit size as equity changes.

## Pyramiding, stops, exits

- **Add** a unit every **½N** of favourable movement, up to **4 units** per market.
- **Initial stop: 2N** below the last unit's entry (for longs). On each add, the stop for the
  whole position is trailed up to 2N below the most recent unit.
- **Exit** (System 2): the 20-day opposite Donchian channel — *in addition to* the 2N stop.

## Unit / correlation limits (risk governance)

| Scope | Max units |
|-------|-----------|
| Single market | 4 |
| Closely correlated markets | 6 |
| Loosely correlated markets | 10 |
| Single direction (all long or all short) | 12 |

**Drawdown scaling:** cut unit size **20% for every 10%** of account drawdown; restore as equity
recovers. (The live book did the *inverse* — added full-size units while down 18–24% on margin.
See the audit.)

## Why real diversification matters here

The turtles traded **futures across independent asset classes** — rates, FX, metals, grains,
energy — that trend on their own clocks. Twenty-five high-[[ATR%]] *equity* ETFs are **one
trade** (equity beta) and breach their channels in the same week. "Varied" must mean asset
classes, not shades of tech. This is the single most important design fix for the v2 ETF book.

## Application in this sleeve (v2, from 2026-07-20)

Target: ~25 varied high-[[ATR%]] ETFs across asset classes, 55-day Donchian breakout entry,
20-day exit, **N-based sizing** (currently absent), 2N stop, unit/correlation caps above.
Open design questions (leverage arithmetic, the pullback-to-10ema filter that truncates the
right tail) are worked in [[total trading system v1 runs turtle exits without turtle sizing]].

## Automation (Pillar 2 — mechanical execution)

Every Donchian level is computable and IBKR supports resting stops. Set them and TradingView
`alertcondition` alerts; a channel exit that must be *noticed* will be missed (6981's exit sat
three sessions unexecuted). If it isn't automated, it isn't a system.

## Benchmark (for the gradebook)
Judge against a trend-following / managed-futures benchmark, not SPY. Pillar 3, action 2.

## Related
- [[Momentum discretion]] — the discretionary-momentum counterpart in the odyssey
- [[total trading system v1 runs turtle exits without turtle sizing]] — live audit of this book
- [[leveraged ETF]] — compute N and channels on the underlying, execute in the wrapper
- [[HARADA (MOC)]] · [[celebrated investor]] · [[multi strategy fund]]

## Primary source — exact core rules & TradingView implementation

The rules above are the operational summary for this sleeve. The verbatim mechanics below are
the documented original ruleset (Faith / Covel reconstructions) plus a TradingView
implementation path — kept as the backtestable source of truth.

### N (volatility unit) — exact formula

N is the 20-day exponential moving average of True Range.
True Range = max(high − low, high − previous close, previous close − low).
N_t = (N_{t−1} × 19/20) + (True Range_t / 20). Seed with a 20-day simple average of True Range.

### Unit sizing (risk management)

One unit is sized to risk ~1% of current account equity.
Unit size = (Account Equity × 0.01) / (N × dollars-per-point / contract multiplier).
This normalizes risk across markets: volatile contracts get smaller positions, quiet ones
larger. Pyramiding adds units on favorable moves. Hard limits: max 4 units per single market,
6 in closely correlated markets (same direction), 10 in loosely correlated, 12 units total in
one direction across the portfolio. Equity is reduced (e.g., 20%) after drawdowns to scale down
risk.

### System 1 (shorter-term, more signals)

- Entry: buy when price exceeds (by one tick) the highest high of the preceding 20 days; sell
  short when price drops below the lowest low of the preceding 20 days.
- Skip the signal if the previous System 1 trade was a winner (the position moved 2N in the
  profitable direction before hitting the 10-day exit). If skipped, treat the 55-day breakout as
  the failsafe entry.
- Exit (trailing): for longs, exit all units at the lowest low of the preceding 10 days; for
  shorts, at the highest high of the preceding 10 days.

### System 2 (longer-term, fewer signals, higher conviction)

- Entry: buy on breakout above the highest high of the preceding 55 days (one tick); sell short
  below the lowest low of the preceding 55 days. Take every signal — no skip filter.
- Exit (trailing): for longs, exit all units at the lowest low of the preceding 20 days; for
  shorts, at the highest high of the preceding 20 days.

### Pyramiding

After initial entry, add one additional unit at every +½N favorable price movement (adjusted
for actual fill/slippage). Continue until the per-market maximum (typically 4 units) is reached.
Initial risk is controlled via the unit definition and N-based stops (early stops often
referenced around 2N adverse); the primary protection is the exit channel and position sizing.

### TradingView implementation

1. **Visualization** — daily chart; add the built-in "Donchian Channels" three times: period 20
   (System 1 entry / System 2 exit), period 55 (System 2 entry), period 10 (System 1 exit). Add
   ATR (length 14 or 20) or a custom N (20-day EMA of True Range) in Pine. Breakouts are price
   piercing the band; pyramiding levels are +0.5N increments from entry.
2. **Pine Script (v5) for backtesting & alerts** — `highest_20 = ta.highest(high, 20)`,
   `lowest_20 = ta.lowest(low, 20)`, same for 55 and 10. Detect breakouts:
   `long_entry_s1 = ta.crossover(close, highest_20[1])`. Track skip-logic state with `var`.
   Unit qty ≈ `math.floor((strategy.equity * risk_pct) / (N * point_value))`.
   `strategy.entry("S1 Long", strategy.long, qty=units)` on breakout;
   `strategy.exit()` / `strategy.close()` on the opposing Donchian level. Add pyramiding on
   +0.5N moves under unit limits. `alertcondition(breakout_condition, title="Turtle S1 Long
   Breakout", message="...")` for notifications. Public scripts reach 80–90% fidelity for
   single-symbol testing; full pyramiding + exact skip logic + N-based dynamic qty needs
   careful state management. Model commissions and slippage in strategy settings.
3. **Scanning** — TradingView's screener handles simple filters but not stateful logic
   ("previous S1 winner + current Donchian breakout + N"). For production scanning across
   hundreds of symbols, replicate the Pine logic in a Python scanner (`pandas_ta` + historical
   data). On-chart workflow: apply the Turtle indicator across a watchlist layout and watch for
   fresh breakouts or approaches to channel edges with supportive N.

### A real-world variant — Peter Brandt ([[peter brandt]], The Factor Report)

Peter Brandt runs a variant of this system: into one weekend he held bets on higher wheat
(KC/Chicago), a higher US Dollar, lower Eurocurrency, GBP gaining on the Euro, higher EU bank
stocks, lower US live cattle, higher Italian stocks, and higher Singapore stocks — the same
cross-asset-class, channel-breakout structuring the AWARENESS watchlist alert system uses.
