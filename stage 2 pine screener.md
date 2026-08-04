---
type: setup
description: Pine Script v6 screener for early Stage 2 (Weinstein) stocks — v2 requires the Weinstein core (price above rising 30wma, ≥26wk base before the 30/50 cross, 2× volume surge, Mansfield RS) with the original MA-geometry conditions as toggleable overlays; v1 kept below as legacy.
tags: [screening, rebirth, momentum, breakout]
sleeve: momentum
status: draft
---

# stage 2 pine screener

Finds **early [[stan weinstein]] Stage 2** candidates on the weekly chart. "wma" here means **weekly simple moving average** (e.g. 30wma = 30-week SMA), not a weighted MA.

Strongly linked to the [[rebirth trade]] — an early Stage 2 is exactly the moment a beaten-down stock exits its Stage 1 base. See also [[custom candle stage analysis]] and [[key moving averages]].

## v2 — Weinstein core + empirical overlays (current)

The original five conditions (kept below as v1) are all MA geometry — lagging *consequences* of a Stage 2. None of Weinstein's causal inputs were checked: price vs. the 30wma, a preceding Stage 1 base, breakout volume, or [[relative strength]]. v1 could flag a stock in freefall (price never referenced), re-flag mature trends on mid-trend 30/50 wobbles, and select low-volume dead-cat bounces. v2 fixes this in four tiers:

**Tier 1 — Weinstein core (always required)**

| Condition | Rationale |
|---|---|
| Price > 30wma, 30wma rising (normalized slope over 4–8 wks) | "Never buy below a declining 30-week MA" — his explicit floor |
| 30wma below 50wma for ≥ 26 wks before the cross | Makes "early" real: a decline + base must precede the rebirth; kills re-trigger false positives |
| Volume ≥ 2× the 10-wk average on an up week, within the last N wks | The institutional signature separating accumulation from drift |
| Mansfield RS (52-wk, per-market benchmark) rising and ≥ 0 or crossing zero recently | His most quantifiable Stage 2A tell; separates rebirth leaders from beta bounces |

**Tier 2 — empirical overlays (toggles; my studied fingerprint of past Stage 2s, not Weinstein):** fresh 30/50 cross within 8 wks, 10>20>30>50 stack, 50wma rising (all on by default); below 150/200wma and normalized gap-tightening (off by default).

**Tier 3 — extension governor:** reject candidates > max % above the 30wma, so the screen never serves runaway names that already violate the [[7-11 rule maximum entry ATR% multiple]].

**Tier 4 — sortable quality columns instead of a lone binary:** score 0–10, Mansfield RS value, volume ratio, % above 30wma, % below 104-wk high (overhead supply), weeks since cross, Stage 1 base length — grade candidates the way [[five star setups]] grades entries.

## How to read the screener output

Every `plot()` in the script becomes a column in the Pine Screener. Columns come in two kinds: **binary flags** (1 = condition passed, 0 = failed — filter with `= 1` to find positives) and **numeric scales** (sort on them to rank candidates; the number itself carries meaning).

### Binary columns (filter `= 1` for a hit)

| Column | What it checks | Purpose |
|---|---|---|
| **Stage 2 (all filters)** | Weinstein core **and** every enabled Tier 2 toggle **and** the extension governor | The main filter — the full-conviction list. Strictest, so often empty; that's by design |
| **Weinstein core** | Only the four Tier 1 conditions (1a–1d below) | The wider net — textbook Stage 2s that missed one of my empirical overlays. Filter on this when "all filters" returns nothing |
| **1a price > rising 30wma** | Close above the 30wma and the 30wma sloping up | Weinstein's floor: never buy below a declining 30-week MA |
| **1b base gate 26wk** | 30wma spent ≥ 26 weeks under the 50wma before the cross | Proves a real Stage 1 base preceded this — rejects mid-trend wobbles masquerading as rebirths |
| **1c volume surge** | A ≥ 2× volume up-week within the last 8 weeks | Institutional accumulation signature — separates a real breakout from low-volume drift |
| **1d Mansfield RS** | RS rising and ≥ 0 (or crossed zero recently) | Leadership vs. the local index — separates rebirth leaders from stocks merely bouncing with the market |

The four 1a–1d flags are **diagnostics**: when a stock you expected fails the scan, read them left to right to see exactly which core condition it missed. Don't filter on them individually — that's what "Weinstein core" is for.

### Numeric columns (sort, don't just filter)

| Column | Scale | How to read it |
|---|---|---|
| **Score (0-10)** | 0–10, integer | Count of all ten conditions passed (4 core + 5 overlays + extension). 10 = perfect; 8–9 = near-miss worth eyeballing on the chart. Sort descending. This is the anti-binary: a 9 that failed only the fresh-cross window may still be the best chart in the list. (If the RS toggle is off, 1d counts as a free pass — scores inflate by up to 1) |
| **Mansfield RS** | Unbounded, zero-centered | > 0 = outperforming its market index over the ~52-week frame; < 0 = lagging. Most Stage 2A breakouts happen above zero or the week it's crossed. Higher = stronger leader. Sort descending for leadership |
| **Vol ratio (wk/10wk)** | ~0.2–10+, 1 = average | This week's volume ÷ prior 10-week average. 1 = normal, ≥ 2 = surge threshold, 3+ = textbook Weinstein breakout volume. Note it shows the *current* week only — a stock that surged 3 weeks ago can read 0.8 here and still pass 1c |
| **% above 30wma** | 0%+ (negative = below) | Extension. Near 0 = tight to the trend line (best entries); > 20 = rejected by the governor by default. Sort **ascending** — this is the [[7-11 rule maximum entry ATR% multiple]] proxy: lowest = most buyable |
| **% below 104wk high** | 0–90%+ | Overhead supply. 0 = at a 2-year high, nothing but air above (ideal); 60%+ = years of trapped sellers to chew through. Lower = cleaner rebirth |
| **Weeks since 30/50 cross** | 0–999 | Freshness. 0–8 = inside the fresh-cross window; 999 = the cross never happened in available history. Sort ascending for the earliest transitions |
| **Stage 1 base length (wks)** | 0+ | Weeks the 30wma spent under the 50wma before the most recent cross. ≥ 26 required by gate 1b; the longer the base, the bigger the move — a 100-week base outranks a 30-week base at the same score |

### Suggested reading order

1. Filter **Stage 2 (all filters) = 1**. Anything here passed everything — go straight to the charts.
2. If empty (normal in most weeks), filter **Weinstein core = 1**, sort by **Score** descending, and eyeball the 8s and 9s — check *which* condition they missed via the 1a–1d flags and Tier 2 toggles before dismissing.
3. Tie-break equal scores with the quality columns: lowest **% above 30wma** first (buyable now), then highest **Mansfield RS** (strongest leader), then longest **Stage 1 base length** (biggest potential).
4. The screener output is a *shortlist, not a signal* — every survivor still gets scored with [[five star setups]] and checked against the [[market trend model]] regime before any order.

## Pine Script v2

```pine
//@version=6
indicator("Stage 2 Screener v2 (Weinstein core + overlays)", shorttitle = "Stage2v2", overlay = false)

// ─── IMPORTANT: run on the 1W timeframe. Scan after Friday's close —
// conditions flip mid-week on the live weekly bar. ───
if not timeframe.isweekly
    runtime.error("Set the timeframe to 1W — all moving averages are weekly.")

// ── Inputs: Tier 1 — Weinstein core (always required) ───────────────
slopeLen        = input.int(6,     "MA slope lookback (weeks)", minval = 4, maxval = 8)
minSlopePct     = input.float(0.0, "Min 30wma slope (% over lookback)", step = 0.1)
baseGateWeeks   = input.int(26,    "30wma below 50wma for at least (weeks) before cross", minval = 4)
volWindow       = input.int(8,     "Volume surge within (weeks)", minval = 1)
volMult         = input.float(2.0, "Volume surge multiple of 10wk average", step = 0.1)
rsEnable        = input.bool(true, "Mansfield RS filter (request.security — verify screener support)")
rsBenchOverride = input.symbol("", "RS benchmark override (blank = auto per market)")
rsRiseLen       = input.int(4,     "Mansfield RS rising over (weeks)", minval = 1)
rsZeroLookback  = input.int(8,     "…or Mansfield RS crossed zero within (weeks)", minval = 1)

// ── Inputs: Tier 2 — empirical overlays (studied fingerprint) ───────
useFreshCross     = input.bool(true,  "Require 30/50wma cross within N weeks")
crossLookback     = input.int(8,      "Cross within (weeks)", minval = 1)
useStack          = input.bool(true,  "Require 10>20>30>50 stack")
use50Rising       = input.bool(true,  "Require 50wma rising (normalized)")
requireBelowLT    = input.bool(false, "Require 30/50wma below 150wma & 200wma (earliest rebirths)")
requireGapTighten = input.bool(false, "Require 150-200wma gap tightening (normalized, persistent)")
gapLookback       = input.int(4,      "Gap tightening lookback (weeks)", minval = 2)

// ── Inputs: Tier 3 — extension governor ─────────────────────────────
useExtGovernor = input.bool(true,   "Reject if too extended above 30wma")
maxExtPct      = input.float(20.0,  "Max % above 30wma", step = 0.5)

// ── Weekly moving averages ──────────────────────────────────────────
ma10  = ta.sma(close, 10)
ma20  = ta.sma(close, 20)
ma30  = ta.sma(close, 30)
ma50  = ta.sma(close, 50)
ma150 = ta.sma(close, 150)
ma200 = ta.sma(close, 200)

// ── Tier 1a: price above a rising 30wma (normalized slope) ──────────
slope30Pct = 100 * (ma30 - ma30[slopeLen]) / ma30[slopeLen]
slope50Pct = 100 * (ma50 - ma50[slopeLen]) / ma50[slopeLen]
t1a = close > ma30 and slope30Pct > minSlopePct

// ── Tier 1b: preceding-decline gate ─────────────────────────────────
// At the most recent 30/50 crossover, the 30wma must have spent
// >= baseGateWeeks below the 50wma — i.e. a real Stage 1 preceded it.
var int belowStreak = 0
var int lastBaseLen = na
crossUp3050 = ta.crossover(ma30, ma50)
if crossUp3050
    lastBaseLen := belowStreak
belowStreak := ma30 < ma50 ? belowStreak + 1 : 0
weeksSinceCross = nz(ta.barssince(crossUp3050), 999)
t1b = not na(lastBaseLen) and lastBaseLen >= baseGateWeeks

// ── Tier 1c: volume surge on an up week within the window ───────────
volAvg10   = ta.sma(volume, 10)
volRatio   = volAvg10[1] > 0 ? volume / volAvg10[1] : na
volSurgeWk = not na(volRatio) and volRatio >= volMult and close > close[1]
t1c = nz(ta.barssince(volSurgeWk), 999) <= volWindow

// ── Tier 1d: Mansfield Relative Strength (52-week, weekly) ──────────
// MRS = 100 * (DRS / SMA(DRS, 52) - 1), DRS = close / benchmark close.
// Benchmark auto-selected per market via the symbol's currency.
autoBench = switch syminfo.currency
    "KRW" => "KRX:KOSPI"
    "JPY" => "TSE:TOPIX"
    "TWD" => "TWSE:TAIEX"
    "HKD" => "TVC:HSI"
    => "SP:SPX"
benchSym   = rsBenchOverride == "" ? autoBench : rsBenchOverride
benchClose = request.security(benchSym, timeframe.period, close)
drs = close / benchClose
mrs = 100 * (drs / ta.sma(drs, 52) - 1)
mrsRising = mrs > mrs[rsRiseLen]
mrsZeroOk = mrs >= 0 or nz(ta.barssince(ta.crossover(mrs, 0)), 999) <= rsZeroLookback
t1d = not rsEnable or (not na(mrs) and mrsRising and mrsZeroOk)

// ── Tier 2: empirical overlays ──────────────────────────────────────
t2a = weeksSinceCross <= crossLookback
t2b = ma10 > ma20 and ma20 > ma30 and ma30 > ma50
t2c = slope50Pct > 0
t2d = ma30 < ma150 and ma30 < ma200 and ma50 < ma150 and ma50 < ma200
gapPct = 100 * math.abs(ma150 - ma200) / close
t2e = ta.falling(gapPct, gapLookback)

// ── Tier 3: extension governor & overhead supply ────────────────────
pctAbove30 = 100 * (close - ma30) / ma30
hi104      = ta.highest(close, 104)
pctBelowHi = 100 * (hi104 - close) / hi104
extOk = not useExtGovernor or pctAbove30 <= maxExtPct

// ── Composite ───────────────────────────────────────────────────────
core = t1a and t1b and t1c and t1d
full = core and
     (not useFreshCross     or t2a) and
     (not useStack          or t2b) and
     (not use50Rising       or t2c) and
     (not requireBelowLT    or t2d) and
     (not requireGapTighten or t2e) and
     extOk

score = (t1a ? 1 : 0) + (t1b ? 1 : 0) + (t1c ? 1 : 0) + (t1d ? 1 : 0) +
     (t2a ? 1 : 0) + (t2b ? 1 : 0) + (t2c ? 1 : 0) + (t2d ? 1 : 0) + (t2e ? 1 : 0) +
     (pctAbove30 <= maxExtPct ? 1 : 0)

// ── Screener columns (filter/sort on these in the Pine Screener) ────
plot(full ? 1 : 0,          "Stage 2 (all filters)")
plot(core ? 1 : 0,          "Weinstein core")
plot(score,                 "Score (0-10)")
plot(mrs,                   "Mansfield RS")
plot(volRatio,              "Vol ratio (wk/10wk)")
plot(pctAbove30,            "% above 30wma")
plot(pctBelowHi,            "% below 104wk high")
plot(weeksSinceCross,       "Weeks since 30/50 cross")
plot(nz(lastBaseLen, 0),    "Stage 1 base length (wks)")
plot(t1a ? 1 : 0,           "1a price > rising 30wma")
plot(t1b ? 1 : 0,           "1b base gate 26wk")
plot(t1c ? 1 : 0,           "1c volume surge")
plot(t1d ? 1 : 0,           "1d Mansfield RS")

// ── Chart overlay for eyeballing candidates ─────────────────────────
plot(ma10,  "10wma",  color.new(color.aqua,   0), force_overlay = true)
plot(ma20,  "20wma",  color.new(color.green,  0), force_overlay = true)
plot(ma30,  "30wma",  color.new(color.orange, 0), force_overlay = true)
plot(ma50,  "50wma",  color.new(color.red,    0), force_overlay = true)
plot(ma150, "150wma", color.new(color.gray,   0), force_overlay = true)
plot(ma200, "200wma", color.new(color.black,  0), force_overlay = true)
bgcolor(full ? color.new(color.green, 80) : core ? color.new(color.teal, 88) : na, force_overlay = true)
```

### v2 open items (verify before trusting)

1. **`request.security` in the Pine Screener is unverified.** If the screener rejects the script, set the Mansfield RS toggle off *and* delete the Tier 1d block (the call itself may count against limits even when gated).
2. **Benchmark tickers** — verified 2026-07: `TSE:TOPIX` (not `TVC:TOPIX`, which errors), `TWSE:TAIEX`, `TVC:HSI` all confirmed on TradingView; `KRX:KOSPI` and `SP:SPX` untested in the screener but standard. The override input is the escape hatch.
3. **Volume semantics for KR/JP**: lot structures and holiday weeks distort weekly averages; if 2× misfires there, consider a percentile-rank variant.
4. **Backtest against the study set**: run the `bgcolor` history over the past Stage 2s that produced the v1 five conditions. If the Weinstein core + fingerprint doesn't light up on those charts, the parameters are wrong — find out in an afternoon, not with capital.

## v1 — original five conditions (legacy, kept for reference)

| # | Condition | Required? |
|---|---|---|
| 1 | 30wma crossed above 50wma within the past 8 weeks | ✅ |
| 2 | 30wma **and** 50wma still below 150wma and 200wma | optional (input toggle) |
| 3 | 10wma > 20wma > 30wma > 50wma (stacked) | ✅ |
| 4 | 50wma pointing up | ✅ |
| 5 | Gap between 150wma and 200wma tightening | optional (input toggle) |

Conditions 2 and 5 mark the *earliest* rebirths (long-term MAs still overhead but converging). They are off by default — turn them on to narrow the scan to the freshest Stage 1 → Stage 2 transitions.

**Why superseded**: no price condition (could flag a stock in freefall), no volume, no relative strength, "early" not enforced (re-fires on mid-trend 30/50 wobbles with no preceding base), the 50wma slope check (`ma50 > ma50[2]`) is noise-level, and the 150/200-week MAs are not Weinstein's (his long MAs are 150/200-**day** ≈ 30w/40w) — they survive in v2 only as my own empirical overlay. The 10>20>30>50 stack is closer to a Minervini trend template than to anything in *Secrets for Profiting*.

## Pine Script v1 (legacy)

```pine
//@version=6
indicator("Early Stage 2 Screener (Weinstein)", shorttitle = "Stage2", overlay = false)

// ─── IMPORTANT: run on the 1W timeframe. All MAs are weekly SMAs. ───
if not timeframe.isweekly
    runtime.error("Set the timeframe to 1W — all moving averages are weekly.")

// ── Inputs ──────────────────────────────────────────────────────────
crossLookback     = input.int(8,     "30wma crossed above 50wma within (weeks)", minval = 1)
requireBelowLT    = input.bool(false, "Require 30/50wma below 150wma & 200wma (earliest rebirths)")
requireGapTighten = input.bool(false, "Require 150–200wma gap tightening")
gapLookback       = input.int(4,     "Gap tightening lookback (weeks)", minval = 1)
slopeLookback     = input.int(2,     "50wma rising over (weeks)", minval = 1)

// ── Weekly moving averages ──────────────────────────────────────────
ma10  = ta.sma(close, 10)
ma20  = ta.sma(close, 20)
ma30  = ta.sma(close, 30)
ma50  = ta.sma(close, 50)
ma150 = ta.sma(close, 150)
ma200 = ta.sma(close, 200)

// ── Conditions ──────────────────────────────────────────────────────
// 1. 30wma crossed above 50wma within the past N weeks
weeksSinceCross = ta.barssince(ta.crossover(ma30, ma50))
c1 = not na(weeksSinceCross) and weeksSinceCross <= crossLookback

// 2. (optional) 30wma & 50wma still below both long-term MAs
c2 = ma30 < ma150 and ma30 < ma200 and ma50 < ma150 and ma50 < ma200

// 3. Short MAs stacked: 10 > 20 > 30 > 50
c3 = ma10 > ma20 and ma20 > ma30 and ma30 > ma50

// 4. 50wma pointing up
c4 = ma50 > ma50[slopeLookback]

// 5. (optional) 150–200wma gap tightening
gap = math.abs(ma150 - ma200)
c5 = gap < gap[gapLookback]

earlyStage2 = c1 and c3 and c4 and
     (not requireBelowLT    or c2) and
     (not requireGapTighten or c5)

// ── Screener columns (filter on these in the Pine Screener) ─────────
plot(earlyStage2 ? 1 : 0,          "Early Stage 2")
plot(nz(weeksSinceCross, 999),     "Weeks since 30/50 cross")
plot(c1 ? 1 : 0,                   "1 cross <= N wks")
plot(c2 ? 1 : 0,                   "2 below 150/200")
plot(c3 ? 1 : 0,                   "3 stacked 10>20>30>50")
plot(c4 ? 1 : 0,                   "4 50wma rising")
plot(c5 ? 1 : 0,                   "5 gap tightening")

// ── Chart overlay for eyeballing candidates ─────────────────────────
plot(ma10,  "10wma",  color.new(color.aqua,   0), force_overlay = true)
plot(ma20,  "20wma",  color.new(color.green,  0), force_overlay = true)
plot(ma30,  "30wma",  color.new(color.orange, 0), force_overlay = true)
plot(ma50,  "50wma",  color.new(color.red,    0), force_overlay = true)
plot(ma150, "150wma", color.new(color.gray,   0), force_overlay = true)
plot(ma200, "200wma", color.new(color.black,  0), force_overlay = true)
bgcolor(earlyStage2 ? color.new(color.green, 85) : na, force_overlay = true)
```

## How to run it in the Pine Screener

1. TradingView → **Pine Editor** → paste the **v2** script → **Save** as `Stage 2 Screener v2 (Weinstein core + overlays)` (a saved personal script is enough, no publishing needed).
2. Open the **Pine Screener**: [tradingview.com/pine-screener](https://www.tradingview.com/pine-screener/) (paid plan feature).
3. **Choose script** → pick the saved indicator. **Watchlist** → select the watchlist to scan.
4. Set **Timeframe = 1W** (the script throws an error on any other timeframe on purpose). **Scan after Friday's close** — the live weekly bar flips conditions mid-week.
5. Add a filter: **Stage 2 (all filters) = 1** → **Scan**. Or filter **Weinstein core = 1** and sort by **Score (0-10)** descending to see near-misses instead of a lone binary.
6. Secondary sorts: **Weeks since 30/50 cross** ascending (freshest transitions), **% above 30wma** ascending (least extended), **Mansfield RS** descending (strongest leaders).
7. To restrict to the earliest rebirths, toggle on the 150/200wma overlays in the script settings; if the screener rejects the script, see v2 open item 1 (the `request.security` call).

## Caveats

- The 200wma needs ~4 years of weekly history; recent IPOs return `na` there, so the 150/200 overlays silently exclude them when toggled on — and recent-IPO busts are a rich rebirth hunting ground. The Weinstein core needs ~1 year (52 weeks for Mansfield RS, 26+ weeks for the base gate).
- The screener flags the *setup*, not the entry. Sector strength and overall market stage remain discretionary (Weinstein's market → sector → stock sequence); score candidates with [[five star setups]] like any other, and respect the [[market trend model]] regime.
- The Mansfield RS zero line is the flattened 52-week MA of the RS line; most Stage 2A breakouts occur above it or the week it's crossed — that's why Tier 1d accepts either state.
- Whole-market scans aren't supported by the Pine Screener — it only runs against a watchlist (up to ~1,000 symbols), which fits the intended watchlist workflow here.

## Related
- [[rebirth trade]]
- [[custom candle stage analysis]]
- [[key moving averages]]
- [[Momentum discretion]]
