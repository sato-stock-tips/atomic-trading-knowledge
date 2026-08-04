---
type: concept
description: Leveraged ETFs reduce the capital locked in a trade while keeping the same exposure — Jeff Sun's framework, adopted 2026-07. Size by ADR%, trade off the underlying's chart, filter hard on liquidity.
tags: [trading, sizing, risk, momentum]
status: evergreen
---

# leveraged ETF

Leveraged ETFs let you hold the same exposure with less capital locked in. This is Jeff
Sun's (CFTe, @jfsrev) core argument, which I share: it is "using $2 to make $4 instead of
$4 to make $4" — synthetic leverage without margin borrowing cost. Freed capital stays
available for other setups, which matters in a concentrated book like [[Momentum discretion]].

## Capital efficiency logic

- Sizing keys off ADR% (average daily range), the cousin of [[ATR%]]: every doubling of a
  security's ADR% roughly halves the capital that must be locked up for the same trade
  idea, because the stop distance in [[position size]] terms is reached with fewer dollars
  of notional.
- Given the choice between an underlying and its leveraged ETF, the higher-ADR% vehicle
  wins (a preference Sun attributes to Qullamaggie as well). A liquid mega-cap grinding at
  2% ADR can be traded via its 2×/3× wrapper instead of tying up 3× the capital.
- Risk per trade does not change: the 0.20–0.30% equity risk from [[five star setups]]
  still governs. What changes is notional locked, not risk taken.

## Selection filters (liquidity is non-negotiable)

- Sun tracks only ~30 of ~147 US-listed leveraged ETFs: those whose holdings have high
  ADR% and which trade **over $100M average dollar volume**; his liquid-mega focus list
  requires **$1B+ 50-day average dollar volume**. Illiquid wrappers are untradeable
  regardless of the idea.
- No single-name biotech exposure ever — gap risk defeats stops. Sector exposure to
  IBB/XBI is expressed only through LABU/LABD.

## Execution rules

- **Trade the underlying's chart, not the wrapper's.** TQQQ entries and exits come from
  the QQQ chart. Daily-reset compounding skews the LETF's own moving averages, so
  [[key moving averages]] and [[bjorgum key levels]] are read on the underlying; the
  wrapper is only the execution vehicle.
- **Shorts become longs in inverse ETFs.** Sun expresses every short idea as a long
  position in an inverse/leveraged-inverse ETF (see [[shorts]]). This caps maximum loss at
  the position value — no borrow, no squeeze, no margin call — which fits the commandment
  to always cap downside risk.
- Crypto-linked LETFs (underlying trades 24/7): measure high-of-day distance against the
  trading-session HOD only, and beware Friday-session settlement quirks.

## The decay caveat

Daily reset means volatility drag in choppy, directionless tape: a 2× wrapper of a flat
underlying loses money. Leveraged ETFs are for **trending conditions only** — green regime
per [[market trend model]], trending sector per [[industry (previously known as sectors)]].
In chop they must be cut, not held; [[avoid choppy price action]] applies with double force.

## Own-book evidence (Fast Book, H1 2026)

Net +$43k across 14 leveraged/single-stock ETFs — but concentrated in SNXX (+$41k) and
SOXL (+$20k), both ridden as trends. Eight of fourteen lost money, and MUU (−$8k) plus
INTW (−$3.4k) were held through chop — the decay caveat priced in real money. The tool
works when the trend condition is respected and bleeds when it is not.

Sources: Jeff Sun's [X thread on leveraged ETF perks](https://x.com/jfsrev/status/1946426384106377492),
[liquid LETF list-building](https://x.com/jfsrev/status/1986252984481960281), and the
[Complete Traders' Guide](https://jfsrev.substack.com/p/my-trading-tools-process-routine) on Substack.
