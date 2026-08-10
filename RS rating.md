
# v2 — 8-Week RS Rating variant (Aug 2026)

### What this version adds, in plain terms

The original script above answers one question: *"Over the last year, has this
stock beaten the S&P 500?"* That's useful, but a full year is a long memory — a
stock that had a monster run in January and has been dead money since June can
still carry a high rating in October, because the January gain is still baked
into the 12-month blend. For [[momentum discretion]] trading, where positions are
held for weeks and churn is weekly, a rating that reacts mainly to something that
happened nine months ago is stale information.

v2 doesn't delete that original 12-month rating — it's still calculated the same
way as always, and it's still fully available in Pine Screener and TradingView's
Data Window. Instead, it bolts on a **second, independent rating** that asks a
narrower question: *"Over the last 8 weeks — not the last year — has this stock
beaten the S&P 500?"* — and, as of this revision, that 8-week number is what
actually appears **on the chart itself** when you drop this indicator onto a
symbol, replacing the 12-month number in that one visible spot. See "Graphical
display vs. screener output" below for exactly what that means and why it was
necessary.

### Graphical display vs. screener output — what actually changed here

The first draft of this v2 script made a mistake worth naming explicitly: it
added the 8-week rating only as a `display=display.data_window` plot. That
display mode is correct for Pine Screener (a screener column needs to be a plot,
full stop), but it is **invisible everywhere else** — it does not draw on the
chart, it does not appear in the indicator's pane, it only shows up in
TradingView's Data Window panel (the small sidebar you open manually) or when
this script is run as a Pine Screener filter across a watchlist. So the previous
revision was technically correct but practically useless if your workflow is
"look at one chart and read the number off it," which is how this indicator was
actually meant to be used day to day — the on-chart label is the whole point of
having an RS pane at all.

The fix is to change what feeds the **label** — the only element in this script
that renders a human-readable number directly on the chart. Before this
revision, `labelText1`/`labelText2` were built from `totalRsRating` (the
12-month rating). Now they're built from `totalRsRating8w` (the 8-week rating),
and the label text itself was changed from `' RS Rating'` to `' RS Rating [8w]'`
so nobody mistakes it for the classic 52-week IBD-style number at a glance. The
12-month rating keeps computing in the background and keeps feeding the Pine
Screener plots exactly as before — nothing about the screener functionality
changed. What changed is narrow and specific: the one number stamped visually
next to the RS Line on the chart pane switched from a 12-month lookback to an
8-week lookback.

One deliberate limitation worth stating plainly: **the label can only show one
number at a time.** There's no version of this where both ratings appear
graphically side by side on the chart without cluttering the pane — the RS Line
itself is plotted on a price-derived scale (in the hundreds, driven by
`SpxValue` and `offset`), while a 1–99 rating lives on a completely different
scale, so it can't be drawn as a second line in the same pane without an
awkward dual-axis hack that would confuse more than it clarifies. A text label
sitting at a fixed screen position, showing one number, is the only clean way
this script has ever surfaced the rating visually — which is exactly why
picking *which* rating gets that one visible slot mattered enough to fix.

### How the underlying score is calculated

Strip away the Pine syntax and the 8-week score is doing something a spreadsheet
could do in two cells:

1. Take the stock's closing price today, and its closing price 40 trading days
   ago (40 trading days ≈ 8 calendar weeks, since markets trade roughly 5 days a
   week). Divide today's price by the price 40 days ago. If the stock is up 15%
   over that stretch, this ratio is 1.15.
2. Do the exact same division for the S&P 500 (ticker `SP:SPX`) over the same
   40-day window. If the index is flat, its ratio is 1.00.
3. Divide the stock's ratio by the index's ratio, then multiply by 100. In this
   example: 1.15 / 1.00 × 100 = **115**. A score of exactly 100 means the stock
   moved in lockstep with the S&P 500 over the last 8 weeks — no better, no
   worse. Above 100 means it outperformed; below 100 means it underperformed.
   A stock that fell 10% while the index was flat would score roughly 90.

This is the same arithmetic idea as the original 12-month version, just with one
window (40 trading days) instead of a weighted blend of four windows (63, 126,
189, and 252 trading days — roughly 3, 6, 9, and 12 months). The original blend
leans most heavily on the most recent quarter (a 40% weight) but still lets the
older three quarters (20% each) drag the number around for the better part of a
year. The 8-week version has no long memory at all — a big move from ten weeks
ago has already scrolled out of the window and stops influencing the score.

### From a raw score to a 1–99 rating

A raw score like "115" isn't very intuitive on its own — is that a good number
or a mediocre one? That's what the 1–99 rating is for. It's meant to work like a
percentile: a rating of 90 is meant to say "this stock's performance is better
than roughly 90% of stocks in the market," the same idea IBD (Investor's
Business Daily) popularized with its own RS Rating. The true version of that
calculation needs to compare every stock in the market against every other stock
at once — something Pine Script literally cannot do, because an indicator only
sees the one symbol it's attached to. So this script uses a workaround: a set of
seven fixed "checkpoint" scores (the threshold inputs), each one saying "a raw
score at or above this level corresponds to roughly this percentile." The script
then interpolates between checkpoints with the `f_attributePercentile` function,
which is just a smooth curve-fit between two known points — nothing more exotic
than that. The checkpoints for the 12-month version were tuned (by whoever
originally built this script) against a real cross-section of the market, so
they're a reasonable stand-in for a true percentile rank.

### Why the 8-week version needs its own, different checkpoints

Here's the part that isn't obvious: you cannot reuse the 12-month checkpoints for
the 8-week score, because the two scores don't live on the same scale. A stock
compounding gains over a full year can easily rack up a raw score of 195+ (the
old "99-rating" checkpoint) if it's a real market leader — cumulative moves over
a year get big. But asking a stock to beat the index by 95 percentage points *in
just 8 weeks* is an extreme, rare event — reusing 195 as the 8-week checkpoint
for a 99 rating would mean almost nothing ever reaches 99, and most stocks would
bunch up in the low-middle of the scale regardless of how strong their recent
move actually was. The rating would stop being useful because it would stop
discriminating between stocks.

The fix is a **separate set of seven checkpoints for the 8-week score**
(`RS Rating Thresholds [8 weeks]` in the inputs panel), scaled down from the
originals. The scaling used here is a rule of thumb borrowed from how price
volatility behaves over time: return dispersion tends to grow with the *square
root* of time, not linearly. Roughly speaking, if a 252-trading-day (~1 year)
window produces outperformance spreads of a certain size, a 40-trading-day (~8
week) window — about 1/6th as long — should produce spreads about
√(40/252) ≈ 0.40× as wide. So each checkpoint here was recentered around 100
(the "no difference from the index" point) and its distance from 100 was
multiplied by roughly 0.40. For example, the original 99-rating checkpoint of
195.93 sits 95.93 points above 100; scaled down that becomes 100 + 95.93×0.40 ≈
138.37, which is the 8-week 99-rating checkpoint used below.

**Be clear-eyed about what this is and isn't.** This scaling is an
order-of-magnitude estimate, not a calibration against real market data — nobody
has back-tested what raw 8-week scores actually looked like across hundreds of
stocks and picked checkpoints from the real percentile boundaries. Treat the
seeded numbers as a working starting point that lets the script run today, and
plan to retune them by eye: if you find that almost every strong momentum name
you look at shows a rating of 99, the checkpoints are too loose (lower them); if
almost nothing ever gets above 70 even during an obvious breakout, they're too
tight (raise them).

### Reading the two ratings together

The chart label now shows only the 8-week number, but the 12-month number
hasn't gone anywhere — open Data Window (or add this script as a Pine Screener
filter across a watchlist) and both `RS Rating` (12-month) and
`RS Rating [8 weeks]` are sitting there as separate rows. Pulling both up side
by side, even manually, tells you more than either number alone:

- **High 12-month AND high 8-week** — a stock with a strong long-term trend that
  is *still* accelerating right now. The classic "leader still leading" setup.
- **High 12-month, low/falling 8-week** — a former leader that's cooling off or
  rolling over. The old strength is inertia from months ago, not current price
  action. Worth a second look before assuming it's still a buy.
- **Low 12-month, high 8-week** — a name with no long-term track record (or a
  laggard) that has suddenly caught a strong bid in the last two months. This is
  where early-stage turnarounds and fresh breakouts from names nobody was
  watching tend to show up first.
- **Low on both** — no recent or long-term outperformance. Nothing to see here.

### What was left alone, and why

The **RS Line** (the price-relative-to-SPX line plotted in the pane) and the
**QULLA Base** compression detector are unchanged in v2, and were left alone
even in this graphical revision. That's not an oversight — neither of them is a
fixed-lookback percentile system the way the RS Rating is, so "make it 8 weeks"
doesn't actually apply to them:

- The **RS Line** is just `close / SPX close`, continuously recomputed at every
  bar and scaled for display. It has no lookback window to shorten — it's a
  running ratio, not a trailing-period score. Converting it into an 8-week-only
  line would mean building an entirely different kind of plot (a rolling
  momentum oscillator that resets every 40 bars, rather than a cumulative
  relative-strength curve), which is a different indicator, not a timeframe
  tweak to this one, and wasn't what was asked for.
- **QULLA Base** never used a 12-month or 8-week window in the first place — it
  reacts to Bollinger Band width (20-bar), ATR compression (14-bar), and volume
  dry-up (20-bar), all short-horizon by design. There was nothing "yearly" about
  it to shorten.

So the only piece of this script that had a "previous year setting" to replace
was the RS Rating's percentile calculation and the label that displays it — and
that's exactly the piece this revision changed. This mirrors the reasoning used
to add the 8-week column in [[abc script]] v3.1, extended one step further here
because, unlike the abc script (which only ever surfaced its RS Rating through
Pine Screener, never on-chart), this script has an actual on-chart label — so
"graphically reflect 8 weeks" had a concrete, fixable target.

```pine
//@version=6
indicator(title='RS Rating [8 Weeks] + QULLA Base + Screener v2', shorttitle='RS+BASE+SCR v2 [8W]', overlay=false, max_bars_back=253)

// Constant value
comparativeTickerId = 'SP:SPX'

// Inputs
hideRSRat = input(false, title='Hide Rating', group='RS Line')
ratingOnly = input(false, title='Rating Only', group='RS Line')
colorRS = input(color.rgb(0, 0, 255, 0), title='Color', group='RS Line', inline='a')
lineTicker = input('SP:SPX', title='Comparative Symbol for Line', group='Shape & Offset', tooltip='Reference ticker used for calculation of the RS Line.')
SpxValue = input(4200, title='Approximate Value of Comparative Symbol', group='Shape & Offset', tooltip='Used to gather a constant value')
offset = input.int(80, minval=0, maxval=2000, title='Offset (%)', group='Shape & Offset', tooltip='Used to display the RS Line under the price.')
plotNewHigh = input(true, title='Plot RS New Highs', group='RS Line New High')
rsNewHigh = input.string('RS New Highs', title='Type', options=['RS New Highs', 'RS New Highs Before Price', 'Historical RS New Highs', 'Historical RS New Highs Before Price'], group='RS Line New High', inline='b')
blueDotCol = input(color.rgb(121, 213, 242, 62), title='Color', group='RS Line New High', inline='b')
lookback = input.int(250, title='Look-back', minval=1, maxval=252, group='RS Line New High', tooltip='The lookback for calculation of price and RS New Highs.', inline='b')
sizeLabHigh = input.string('Tiny', title='Size', options=['Tiny', 'Small', 'Normal', 'Large'], group='RS Line New High')
plotNewLow = input(false, title='Plot RS New Lows', group='RS Line New High')
rsNewLow = input.string('Historical RS New Lows', title='Type', options=['RS New Lows', 'RS New Lows Before Price', 'Historical RS New Lows', 'Historical RS New Lows Before Price'], group='RS Line New High', inline='x')
redDotCol = input(color.rgb(255, 82, 82, 62), title='Color', group='RS Line New High', inline='x')
lookback2 = input.int(250, title='Look-back', minval=1, maxval=252, group='RS Line New High', tooltip='The lookback for calculation of price and RS New Lows.', inline='x')
sizeLabLow = input.string('Tiny', title='Size', options=['Tiny', 'Small', 'Normal', 'Large'], group='RS Line New High')
boolMa = input(false, title='Display MA 1 on RS Line', group='1st MA on RS Line')
lenMa = input(21, title='Lenght Da', group='1st MA on RS Line', inline='c')
colMa = input(color.orange, title='Color', group='1st MA on RS Line', inline='c')
typMa = input.string('EMA', title='Type Da', options=['SMA', 'EMA'], group='1st MA on RS Line', inline='c')
lenMaWe = input(10, title='Lenght We', group='1st MA on RS Line', inline='c')
typMaWe = input.string('SMA', title='Type We', options=['SMA', 'EMA'], group='1st MA on RS Line', inline='c')
fillMa = input(false, title='Area Color', group='1st MA on RS Line')
posCol = input(color.rgb(0, 230, 119, 75), title='Positive Area', group='1st MA on RS Line', inline='d')
negCol = input(color.rgb(255, 82, 82, 75), title='Negative Area', group='1st MA on RS Line', inline='d')
boolMa2 = input(false, title='Display MA 2 on RS Line', group='2nd MA on RS Line')
lenMa2 = input(50, title='Lenght Da', group='2nd MA on RS Line', inline='c')
colMa2 = input(color.red, title='Color', group='2nd MA on RS Line', inline='c')
typMa2 = input.string('EMA', title='Type Da', options=['SMA', 'EMA'], group='2nd MA on RS Line', inline='c')
lenMa2We = input(21, title='Lenght We', group='2nd MA on RS Line', inline='c')
typMa2We = input.string('SMA', title='Type We', options=['SMA', 'EMA'], group='2nd MA on RS Line', inline='c')

// RS Rating Thresholds — calibrated from market-wide RS distribution
// These are the RS Score values at each percentile boundary.
// Update periodically to keep ratings accurate.
first = input.float(195.93, title='RS Score for 99+ rating', group='RS Rating Thresholds')
scnd = input.float(117.11, title='RS Score for 90+ rating', group='RS Rating Thresholds')
thrd = input.float(99.04, title='RS Score for 70+ rating', group='RS Rating Thresholds')
frth = input.float(91.66, title='RS Score for 50+ rating', group='RS Rating Thresholds')
ffth = input.float(80.96, title='RS Score for 30+ rating', group='RS Rating Thresholds')
sxth = input.float(53.64, title='RS Score for 10+ rating', group='RS Rating Thresholds')
svth = input.float(24.86, title='RS Score for 1+ rating', group='RS Rating Thresholds')

// RS Rating Thresholds [8 weeks] — first-guess sqrt-time scaling (~0.40x) of the
// thresholds above. NOT backtested; recalibrate by eye once you have real data.
first8w = input.float(138.37, title='RS Score for 99+ rating', group='RS Rating Thresholds [8 weeks]')
scnd8w = input.float(106.84, title='RS Score for 90+ rating', group='RS Rating Thresholds [8 weeks]')
thrd8w = input.float(99.62, title='RS Score for 70+ rating', group='RS Rating Thresholds [8 weeks]')
frth8w = input.float(96.66, title='RS Score for 50+ rating', group='RS Rating Thresholds [8 weeks]')
ffth8w = input.float(92.38, title='RS Score for 30+ rating', group='RS Rating Thresholds [8 weeks]')
sxth8w = input.float(81.46, title='RS Score for 10+ rating', group='RS Rating Thresholds [8 weeks]')
svth8w = input.float(69.94, title='RS Score for 1+ rating', group='RS Rating Thresholds [8 weeks]')

// Blue Dot adjustments
if lookback == 250 and timeframe.isweekly
    lookback := 52
if lookback2 == 250 and timeframe.isweekly
    lookback2 := 52

highLabel = switch sizeLabHigh
    'Normal' => size.normal
    'Tiny' => size.tiny
    'Small' => size.small
    'Large' => size.large

lowLabel = switch sizeLabLow
    'Normal' => size.normal
    'Tiny' => size.tiny
    'Small' => size.small
    'Large' => size.large

n63 = bar_index < 63 ? bar_index : 63
n126 = bar_index < 126 ? bar_index : 126
n189 = bar_index < 189 ? bar_index : 189
n252 = bar_index < 252 ? bar_index : 252
n40 = bar_index < 40 ? bar_index : 40

// Comparative Ticker for RS Line
comparativeSymbol = request.security(lineTicker, timeframe.period, close)
rsCurve = close / comparativeSymbol

if syminfo.industry == 'Investment Trusts/Mutual Funds'
    offset := 90

rsRatio = timeframe.isweekly ? SpxValue * (offset - 10) / 100 : SpxValue * offset / 100
rs = rsCurve * rsRatio

prevlookback = lookback
prevlookback2 = lookback2
lookback := math.min(lookback - 1, bar_index)

rsPlot = plot(rs, title='RS Line', style=plot.style_line, linewidth=1, color=colorRS)

// 1st MA on RS Line
rsMA = ta.sma(rs, lenMa)
if typMa == 'SMA' and not timeframe.isweekly
    rsMA := ta.sma(rs, lenMa)
if typMa == 'EMA' and not timeframe.isweekly
    rsMA := ta.ema(rs, lenMa)
if typMaWe == 'SMA' and timeframe.isweekly
    rsMA := ta.sma(rs, lenMaWe)
if typMaWe == 'EMA' and timeframe.isweekly
    rsMA := ta.ema(rs, lenMaWe)

maPlot = plot(boolMa ? rsMA : na, color=colMa, linewidth=1)
maPlot2 = plot(boolMa and fillMa ? rsMA : na, color=color.rgb(0, 0, 0, 100), linewidth=1)
fillCol = rs > rsMA ? posCol : negCol
fill(rsPlot, maPlot2, color=fillCol)

// 2nd MA on RS Line
rsMA2 = ta.sma(rs, lenMa2)
if typMa2 == 'SMA' and not timeframe.isweekly
    rsMA2 := ta.sma(rs, lenMa2)
if typMa2 == 'EMA' and not timeframe.isweekly
    rsMA2 := ta.ema(rs, lenMa2)
if typMa2We == 'SMA' and timeframe.isweekly
    rsMA2 := ta.sma(rs, lenMa2We)
if typMa2We == 'EMA' and timeframe.isweekly
    rsMA2 := ta.ema(rs, lenMa2We)

maPlot3 = plot(boolMa2 ? rsMA2 : na, color=colMa2, linewidth=1)

// RS New Highs
var label newHigh = na
histNH = ta.highest(rs, prevlookback)
histCl = ta.highest(high, prevlookback)

if rsNewHigh == 'Historical RS New Highs' and plotNewHigh and rs == histNH
    newHigh := label.new(x=bar_index, y=rs, color=blueDotCol, style=label.style_circle, size=highLabel)
if rsNewHigh == 'Historical RS New Highs Before Price' and plotNewHigh and rs == histNH and high < histCl
    newHigh := label.new(x=bar_index, y=rs, color=blueDotCol, style=label.style_circle, size=highLabel)
if barstate.islast and rsNewHigh == 'RS New Highs' and plotNewHigh and rs == histNH
    label.delete(newHigh)
    newHigh := label.new(x=bar_index, y=rs, color=blueDotCol, style=label.style_circle, size=highLabel)
if barstate.islast and rsNewHigh == 'RS New Highs Before Price' and plotNewHigh and rs == histNH and high < histCl
    label.delete(newHigh)
    newHigh := label.new(x=bar_index, y=rs, color=blueDotCol, style=label.style_circle, size=highLabel)

// RS New Lows
var label newLow = na
histNL = ta.lowest(rs, prevlookback2)
histClL = ta.lowest(low, prevlookback2)

if rsNewLow == 'Historical RS New Lows' and plotNewLow and rs == histNL
    newLow := label.new(x=bar_index, y=rs, color=redDotCol, style=label.style_circle, size=lowLabel)
if rsNewLow == 'Historical RS New Lows Before Price' and plotNewLow and rs == histNL and low > histClL
    newLow := label.new(x=bar_index, y=rs, color=redDotCol, style=label.style_circle, size=lowLabel)
if barstate.islast and rsNewLow == 'RS New Lows' and plotNewLow and rs == histNL
    label.delete(newLow)
    newLow := label.new(x=bar_index, y=rs, color=redDotCol, style=label.style_circle, size=lowLabel)
if barstate.islast and rsNewLow == 'RS New Lows Before Price' and plotNewLow and rs == histNL and low > histClL
    label.delete(newLow)
    newLow := label.new(x=bar_index, y=rs, color=redDotCol, style=label.style_circle, size=lowLabel)

// RS Rating calc
closeDa = request.security(syminfo.tickerid, 'D', close)
spxCloseDa = request.security(comparativeTickerId, 'D', close)

perfTicker63 = nz(closeDa / closeDa[n63], 1.0)
perfTicker126 = nz(closeDa / closeDa[n126], 1.0)
perfTicker189 = nz(closeDa / closeDa[n189], 1.0)
perfTicker252 = nz(closeDa / closeDa[n252], 1.0)

perfComp63 = nz(spxCloseDa / spxCloseDa[n63], 1.0)
perfComp126 = nz(spxCloseDa / spxCloseDa[n126], 1.0)
perfComp189 = nz(spxCloseDa / spxCloseDa[n189], 1.0)
perfComp252 = nz(spxCloseDa / spxCloseDa[n252], 1.0)

float rs_stock = 0.4 * perfTicker63 + 0.2 * perfTicker126 + 0.2 * perfTicker189 + 0.2 * perfTicker252
float rs_ref = 0.4 * perfComp63 + 0.2 * perfComp126 + 0.2 * perfComp189 + 0.2 * perfComp252

float totalRsScore = rs_stock / rs_ref * 100
if na(totalRsScore)
    totalRsScore := svth
float totalRsRating = na

// RS Rating thresholds — uses fixed values from inputs (screener-safe)
// request.seed() is NOT compatible with Pine Screener, so thresholds
// are provided as inputs. Update the input values periodically from
// the RSRATING seed ticker on TradingView to keep ratings accurate.

if totalRsScore >= first
    totalRsRating := 99
if totalRsScore <= svth
    totalRsRating := 1

f_attributePercentile(totalRsScore, tallerPerf, smallerPerf, rangeUp, rangeDn, weight) =>
    sum = totalRsScore + (totalRsScore - smallerPerf) * weight
    if sum > tallerPerf - 1
        sum := tallerPerf - 1
    k1 = smallerPerf / rangeDn
    k2 = (tallerPerf - 1) / rangeUp
    k3 = (k1 - k2) / (tallerPerf - 1 - smallerPerf)
    RsRating = sum / (k1 - k3 * (totalRsScore - smallerPerf))
    if RsRating > rangeUp
        RsRating := rangeUp
    if RsRating < rangeDn
        RsRating := rangeDn
    RsRating

if totalRsScore < first and totalRsScore >= scnd
    totalRsRating := f_attributePercentile(totalRsScore, first, scnd, 98, 90, 0.33)
if totalRsScore < scnd and totalRsScore >= thrd
    totalRsRating := f_attributePercentile(totalRsScore, scnd, thrd, 89, 70, 2.1)
if totalRsScore < thrd and totalRsScore >= frth
    totalRsRating := f_attributePercentile(totalRsScore, thrd, frth, 69, 50, 0)
if totalRsScore < frth and totalRsScore >= ffth
    totalRsRating := f_attributePercentile(totalRsScore, frth, ffth, 49, 30, 0)
if totalRsScore < ffth and totalRsScore >= sxth
    totalRsRating := f_attributePercentile(totalRsScore, ffth, sxth, 29, 10, 0)
if totalRsScore < sxth and totalRsScore >= svth
    totalRsRating := f_attributePercentile(totalRsScore, sxth, svth, 9, 2, 0)

// RS Rating calc [8 weeks] — pure 40-trading-day stock-vs-SPX ratio, no
// quarterly blend. Same bucket function, own thresholds (grpRS8W inputs above).
perfTicker8w = nz(closeDa / closeDa[n40], 1.0)
perfComp8w = nz(spxCloseDa / spxCloseDa[n40], 1.0)

float totalRsScore8w = perfTicker8w / perfComp8w * 100
if na(totalRsScore8w)
    totalRsScore8w := svth8w
float totalRsRating8w = na

if totalRsScore8w >= first8w
    totalRsRating8w := 99
if totalRsScore8w <= svth8w
    totalRsRating8w := 1
if totalRsScore8w < first8w and totalRsScore8w >= scnd8w
    totalRsRating8w := f_attributePercentile(totalRsScore8w, first8w, scnd8w, 98, 90, 0.33)
if totalRsScore8w < scnd8w and totalRsScore8w >= thrd8w
    totalRsRating8w := f_attributePercentile(totalRsScore8w, scnd8w, thrd8w, 89, 70, 2.1)
if totalRsScore8w < thrd8w and totalRsScore8w >= frth8w
    totalRsRating8w := f_attributePercentile(totalRsScore8w, thrd8w, frth8w, 69, 50, 0)
if totalRsScore8w < frth8w and totalRsScore8w >= ffth8w
    totalRsRating8w := f_attributePercentile(totalRsScore8w, frth8w, ffth8w, 49, 30, 0)
if totalRsScore8w < ffth8w and totalRsScore8w >= sxth8w
    totalRsRating8w := f_attributePercentile(totalRsScore8w, ffth8w, sxth8w, 29, 10, 0)
if totalRsScore8w < sxth8w and totalRsScore8w >= svth8w
    totalRsRating8w := f_attributePercentile(totalRsScore8w, sxth8w, svth8w, 9, 2, 0)

// On-chart label now shows the 8-WEEK rating, not the 12-month one.
// The 12-month rating (totalRsRating) is still computed above and still
// available in Pine Screener / Data Window — it's only the graphical,
// on-chart label that switched sources.
isDaily = timeframe.isdaily
labelText1 = ' RS Rating [8w]'
labelText2 = ''

if isDaily and not na(totalRsRating8w)
    labelText2 := '\n\n ' + str.tostring(totalRsRating8w, '#0')

if ratingOnly and not na(totalRsRating8w)
    labelText1 := ''
    labelText2 := '\n ' + str.tostring(totalRsRating8w, '#0')

label1 = hideRSRat == false and barstate.islast ? label.new(bar_index, rs, text=labelText1, color=color.rgb(0, 0, 0, 100), size=size.normal, textcolor=colorRS, style=label.style_label_center, textalign=text.align_left, yloc=yloc.price) : na
label2 = hideRSRat == false and barstate.islast ? label.new(bar_index, rs, text=labelText2, color=color.rgb(0, 0, 0, 100), size=size.large, textcolor=colorRS, style=label.style_label_center, textalign=text.align_left, yloc=yloc.price) : na

label.delete(label1[1])
label.delete(label2[1])

// ======================================================
// QULLA Base Forming (Compression)
// ======================================================

bbLen = input.int(20, 'BB Length', group='QULLA Base')
bbMult = input.float(2.0, 'BB Mult', group='QULLA Base')
atrLen = input.int(14, 'ATR Length', group='QULLA Base')
atrPctMax = input.float(5.0, 'ATR% max (US default)', step=0.1, group='QULLA Base')
volLen = input.int(20, 'Volume MA Length', group='QULLA Base')
volMax = input.float(0.85, 'Vol/VolMA max (dry)', step=0.01, group='QULLA Base')
bbWidthPctMax = input.float(7.0, 'BB Width% max (tight)', step=0.1, group='QULLA Base')

emaFastLen = input.int(10, 'Fast EMA Length', group='QULLA Base')
emaMidLen = input.int(20, 'Mid EMA Length', group='QULLA Base')
emaFast = ta.ema(close, emaFastLen)
emaMid = ta.ema(close, emaMidLen)

useAtrCompression = input.bool(true, 'Require ATR% compression vs lookback?', group='QULLA Base')
atrCmpLookback = input.int(5, 'ATR% Compression Lookback (bars)', minval=1, group='QULLA Base')
atrCmpRatio = input.float(0.90, 'ATR% must be <= prior ×', step=0.01, group='QULLA Base')

basis = ta.sma(close, bbLen)
dev = bbMult * ta.stdev(close, bbLen)
upper = basis + dev
lower = basis - dev
bbWidthPct = (upper - lower) / close * 100.0

atr = ta.atr(atrLen)
atrPct = atr / close * 100.0

atrPctPrev = atrPct[atrCmpLookback]
atrCompressionOk = not useAtrCompression ? true : not na(atrPctPrev) and atrPct <= atrPctPrev * atrCmpRatio

volMA = ta.sma(volume, volLen)
volRel = volume / volMA

baseForming = close > emaFast and emaFast > emaMid and bbWidthPct <= bbWidthPctMax and atrPct <= atrPctMax and volRel <= volMax and atrCompressionOk

plot(baseForming ? rs : na, title='QULLA BASE (RS pane)', style=plot.style_circles, linewidth=3, color=color.lime)
plot(emaFast, title='EMA 10 (hidden)', display=display.none)
plot(emaMid, title='EMA 20 (hidden)', display=display.none)

alertcondition(baseForming, title='QULLA_BASE_FORMING', message='QULLA_BASE_FORMING')

// ======================================================
// PINE SCREENER — RS Rating Filtering
// ======================================================
// HOW TO USE IN PINE SCREENER:
//   1. Open TradingView → Stock Screener → Pine Screener tab
//   2. Add this indicator as a filter
//   3. To find RS 99 stocks: filter "RS Rating Is 99" crosses above 0.5
//   4. To filter by RS number: set Min/Max RS Rating inputs, then
//      filter "RS Rating In Range" crosses above 0.5
//   5. To find any stock with RS above a threshold: filter "RS Rating" > 80
//   6. For the 8-week version, use the "[8 weeks]" filters the same way.
//   NOTE: RS Rating uses approximate data for stocks with <12 months history

screenRSMin = input.int(99, title='Min RS Rating', minval=0, maxval=99, group='Screen', tooltip='Minimum RS Rating for range filter. Set 99 to find RS 99 stocks.')
screenRSMax = input.int(99, title='Max RS Rating', minval=1, maxval=99, group='Screen', tooltip='Maximum RS Rating for range filter. Set 99 to find RS 99 stocks.')
screenRSMin8w = input.int(99, title='Min RS Rating [8 weeks]', minval=0, maxval=99, group='Screen', tooltip='Minimum 8-week RS Rating for range filter. Set 99 to find RS 99 stocks.')
screenRSMax8w = input.int(99, title='Max RS Rating [8 weeks]', minval=1, maxval=99, group='Screen', tooltip='Maximum 8-week RS Rating for range filter. Set 99 to find RS 99 stocks.')

// Plot RS Rating as numeric value; na when data unavailable (screener ignores na)
validRS = not na(totalRsRating)
plot(validRS ? totalRsRating : na, title='RS Rating', display=display.data_window)

validRS8w = not na(totalRsRating8w)
plot(validRS8w ? totalRsRating8w : na, title='RS Rating [8 weeks]', display=display.data_window)

// Boolean: RS Rating equals 99 (quick filter for top-rated stocks)
isRS99 = validRS and totalRsRating == 99
plot(isRS99 ? 1 : 0, title='RS Rating Is 99', display=display.data_window)

isRS99_8w = validRS8w and totalRsRating8w == 99
plot(isRS99_8w ? 1 : 0, title='RS Rating Is 99 [8 weeks]', display=display.data_window)

// Boolean: RS Rating within user-defined min/max range
isRSInRange = validRS and totalRsRating >= screenRSMin and totalRsRating <= screenRSMax
plot(isRSInRange ? 1 : 0, title='RS Rating In Range', display=display.data_window)

isRSInRange8w = validRS8w and totalRsRating8w >= screenRSMin8w and totalRsRating8w <= screenRSMax8w
plot(isRSInRange8w ? 1 : 0, title='RS Rating In Range [8 weeks]', display=display.data_window)

// Alert conditions
alertcondition(isRS99, title='RS Rating 99', message='{{ticker}} has RS Rating 99')
alertcondition(isRSInRange, title='RS Rating In Range', message='{{ticker}} RS Rating {{plot_0}} is in range')
alertcondition(isRS99_8w, title='RS Rating 99 [8 weeks]', message='{{ticker}} has 8-week RS Rating 99')
alertcondition(isRSInRange8w, title='RS Rating In Range [8 weeks]', message='{{ticker}} 8-week RS Rating {{plot_1}} is in range')
```

Original source (v5, pre-screener): https://www.tradingview.com/script/pziQwiT2/

---

# Original RS 

The following text is extracted from the provided image regarding the **RS Rating** (Relative Strength Rating):

---

The **RS Rating** (or [[relative strength]] Rating) is a metric that tracks a stock's price performance relative to the rest of the market. Specifically, it looks at a stock's relative strength over the last 52 weeks. It allows you to identify at a glance stocks that are outperforming the market and may be poised for further gains.

Designed for break-out traders, trend followers, and value investors, the RS Rating can help you identify promising opportunities and make informed investment decisions.

**The Rating stands as follow:**

- From **1 (worst) to 99 (best)**.
    
- A **99 rating** means the stock is outperforming 99% of all stocks in terms of relative share price performance over the last 52 weeks.
    

The RS Rating is accompanied by the **RS line**, which is a representation of the progress of the asset against the comparative symbol (here SP500).

Of course this script is inspired by the IBD rating system. The results may be equivalent but it is not guaranteed. This indicator proposes a scoring system in the style of the one proposed by IBD. Indeed for an optimal result, it would be necessary to compare the relative performance of all actions, which is not yet possible on PineScript.

**Here is the formula for calculating the score:**

$RS Score = 40\% * P3 + 20\% * P6 + 20\% * P9 + 20\% * P12$

**With:**

- **P3** = Performance over the last 3 months
    
- **P6** = Performance over the last 6 months
    
- **P9** = Performance over the last 9 months
    
- **P12** = Performance over the last 12 months
    

There is no equivalent solution for the moment on TradingView. The rating score will only appear on the daily timeframe.

For now it's my pleasure to share!

---
the new source code was combined with qulla base

qulla base (developed by my friend erwin) seeks to find periods of [[pullback]] based on a compression of bollinger bands, compressing ATR(14) and less [[volume]] to best identify [[surf trade]]

qulla base will show up with a yellow column on the top pane in charts screenshots

```pine
//@version=6
indicator(title='RS Rating + QULLA Base + Screener', shorttitle='RS+BASE+SCR', overlay=false, max_bars_back=253)

// Constant value
comparativeTickerId = 'SP:SPX'

// Inputs
hideRSRat = input(false, title='Hide Rating', group='RS Line')
ratingOnly = input(false, title='Rating Only', group='RS Line')
colorRS = input(color.rgb(0, 0, 255, 0), title='Color', group='RS Line', inline='a')
lineTicker = input('SP:SPX', title='Comparative Symbol for Line', group='Shape & Offset', tooltip='Reference ticker used for calculation of the RS Line.')
SpxValue = input(4200, title='Approximate Value of Comparative Symbol', group='Shape & Offset', tooltip='Used to gather a constant value')
offset = input.int(80, minval=0, maxval=2000, title='Offset (%)', group='Shape & Offset', tooltip='Used to display the RS Line under the price.')
plotNewHigh = input(true, title='Plot RS New Highs', group='RS Line New High')
rsNewHigh = input.string('RS New Highs', title='Type', options=['RS New Highs', 'RS New Highs Before Price', 'Historical RS New Highs', 'Historical RS New Highs Before Price'], group='RS Line New High', inline='b')
blueDotCol = input(color.rgb(121, 213, 242, 62), title='Color', group='RS Line New High', inline='b')
lookback = input.int(250, title='Look-back', minval=1, maxval=252, group='RS Line New High', tooltip='The lookback for calculation of price and RS New Highs.', inline='b')
sizeLabHigh = input.string('Tiny', title='Size', options=['Tiny', 'Small', 'Normal', 'Large'], group='RS Line New High')
plotNewLow = input(false, title='Plot RS New Lows', group='RS Line New High')
rsNewLow = input.string('Historical RS New Lows', title='Type', options=['RS New Lows', 'RS New Lows Before Price', 'Historical RS New Lows', 'Historical RS New Lows Before Price'], group='RS Line New High', inline='x')
redDotCol = input(color.rgb(255, 82, 82, 62), title='Color', group='RS Line New High', inline='x')
lookback2 = input.int(250, title='Look-back', minval=1, maxval=252, group='RS Line New High', tooltip='The lookback for calculation of price and RS New Lows.', inline='x')
sizeLabLow = input.string('Tiny', title='Size', options=['Tiny', 'Small', 'Normal', 'Large'], group='RS Line New High')
boolMa = input(false, title='Display MA 1 on RS Line', group='1st MA on RS Line')
lenMa = input(21, title='Lenght Da', group='1st MA on RS Line', inline='c')
colMa = input(color.orange, title='Color', group='1st MA on RS Line', inline='c')
typMa = input.string('EMA', title='Type Da', options=['SMA', 'EMA'], group='1st MA on RS Line', inline='c')
lenMaWe = input(10, title='Lenght We', group='1st MA on RS Line', inline='c')
typMaWe = input.string('SMA', title='Type We', options=['SMA', 'EMA'], group='1st MA on RS Line', inline='c')
fillMa = input(false, title='Area Color', group='1st MA on RS Line')
posCol = input(color.rgb(0, 230, 119, 75), title='Positive Area', group='1st MA on RS Line', inline='d')
negCol = input(color.rgb(255, 82, 82, 75), title='Negative Area', group='1st MA on RS Line', inline='d')
boolMa2 = input(false, title='Display MA 2 on RS Line', group='2nd MA on RS Line')
lenMa2 = input(50, title='Lenght Da', group='2nd MA on RS Line', inline='c')
colMa2 = input(color.red, title='Color', group='2nd MA on RS Line', inline='c')
typMa2 = input.string('EMA', title='Type Da', options=['SMA', 'EMA'], group='2nd MA on RS Line', inline='c')
lenMa2We = input(21, title='Lenght We', group='2nd MA on RS Line', inline='c')
typMa2We = input.string('SMA', title='Type We', options=['SMA', 'EMA'], group='2nd MA on RS Line', inline='c')

// RS Rating Thresholds — calibrated from market-wide RS distribution
// These are the RS Score values at each percentile boundary.
// Update periodically to keep ratings accurate.
first = input.float(195.93, title='RS Score for 99+ rating', group='RS Rating Thresholds')
scnd = input.float(117.11, title='RS Score for 90+ rating', group='RS Rating Thresholds')
thrd = input.float(99.04, title='RS Score for 70+ rating', group='RS Rating Thresholds')
frth = input.float(91.66, title='RS Score for 50+ rating', group='RS Rating Thresholds')
ffth = input.float(80.96, title='RS Score for 30+ rating', group='RS Rating Thresholds')
sxth = input.float(53.64, title='RS Score for 10+ rating', group='RS Rating Thresholds')
svth = input.float(24.86, title='RS Score for 1+ rating', group='RS Rating Thresholds')

// Blue Dot adjustments
if lookback == 250 and timeframe.isweekly
    lookback := 52
if lookback2 == 250 and timeframe.isweekly
    lookback2 := 52

highLabel = switch sizeLabHigh
    'Normal' => size.normal
    'Tiny' => size.tiny
    'Small' => size.small
    'Large' => size.large

lowLabel = switch sizeLabLow
    'Normal' => size.normal
    'Tiny' => size.tiny
    'Small' => size.small
    'Large' => size.large

n63 = bar_index < 63 ? bar_index : 63
n126 = bar_index < 126 ? bar_index : 126
n189 = bar_index < 189 ? bar_index : 189
n252 = bar_index < 252 ? bar_index : 252

// Comparative Ticker for RS Line
comparativeSymbol = request.security(lineTicker, timeframe.period, close)
rsCurve = close / comparativeSymbol

if syminfo.industry == 'Investment Trusts/Mutual Funds'
    offset := 90

rsRatio = timeframe.isweekly ? SpxValue * (offset - 10) / 100 : SpxValue * offset / 100
rs = rsCurve * rsRatio

prevlookback = lookback
prevlookback2 = lookback2
lookback := math.min(lookback - 1, bar_index)

rsPlot = plot(rs, title='RS Line', style=plot.style_line, linewidth=1, color=colorRS)

// 1st MA on RS Line
rsMA = ta.sma(rs, lenMa)
if typMa == 'SMA' and not timeframe.isweekly
    rsMA := ta.sma(rs, lenMa)
if typMa == 'EMA' and not timeframe.isweekly
    rsMA := ta.ema(rs, lenMa)
if typMaWe == 'SMA' and timeframe.isweekly
    rsMA := ta.sma(rs, lenMaWe)
if typMaWe == 'EMA' and timeframe.isweekly
    rsMA := ta.ema(rs, lenMaWe)

maPlot = plot(boolMa ? rsMA : na, color=colMa, linewidth=1)
maPlot2 = plot(boolMa and fillMa ? rsMA : na, color=color.rgb(0, 0, 0, 100), linewidth=1)
fillCol = rs > rsMA ? posCol : negCol
fill(rsPlot, maPlot2, color=fillCol)

// 2nd MA on RS Line
rsMA2 = ta.sma(rs, lenMa2)
if typMa2 == 'SMA' and not timeframe.isweekly
    rsMA2 := ta.sma(rs, lenMa2)
if typMa2 == 'EMA' and not timeframe.isweekly
    rsMA2 := ta.ema(rs, lenMa2)
if typMa2We == 'SMA' and timeframe.isweekly
    rsMA2 := ta.sma(rs, lenMa2We)
if typMa2We == 'EMA' and timeframe.isweekly
    rsMA2 := ta.ema(rs, lenMa2We)

maPlot3 = plot(boolMa2 ? rsMA2 : na, color=colMa2, linewidth=1)

// RS New Highs
var label newHigh = na
histNH = ta.highest(rs, prevlookback)
histCl = ta.highest(high, prevlookback)

if rsNewHigh == 'Historical RS New Highs' and plotNewHigh and rs == histNH
    newHigh := label.new(x=bar_index, y=rs, color=blueDotCol, style=label.style_circle, size=highLabel)
if rsNewHigh == 'Historical RS New Highs Before Price' and plotNewHigh and rs == histNH and high < histCl
    newHigh := label.new(x=bar_index, y=rs, color=blueDotCol, style=label.style_circle, size=highLabel)
if barstate.islast and rsNewHigh == 'RS New Highs' and plotNewHigh and rs == histNH
    label.delete(newHigh)
    newHigh := label.new(x=bar_index, y=rs, color=blueDotCol, style=label.style_circle, size=highLabel)
if barstate.islast and rsNewHigh == 'RS New Highs Before Price' and plotNewHigh and rs == histNH and high < histCl
    label.delete(newHigh)
    newHigh := label.new(x=bar_index, y=rs, color=blueDotCol, style=label.style_circle, size=highLabel)

// RS New Lows
var label newLow = na
histNL = ta.lowest(rs, prevlookback2)
histClL = ta.lowest(low, prevlookback2)

if rsNewLow == 'Historical RS New Lows' and plotNewLow and rs == histNL
    newLow := label.new(x=bar_index, y=rs, color=redDotCol, style=label.style_circle, size=lowLabel)
if rsNewLow == 'Historical RS New Lows Before Price' and plotNewLow and rs == histNL and low > histClL
    newLow := label.new(x=bar_index, y=rs, color=redDotCol, style=label.style_circle, size=lowLabel)
if barstate.islast and rsNewLow == 'RS New Lows' and plotNewLow and rs == histNL
    label.delete(newLow)
    newLow := label.new(x=bar_index, y=rs, color=redDotCol, style=label.style_circle, size=lowLabel)
if barstate.islast and rsNewLow == 'RS New Lows Before Price' and plotNewLow and rs == histNL and low > histClL
    label.delete(newLow)
    newLow := label.new(x=bar_index, y=rs, color=redDotCol, style=label.style_circle, size=lowLabel)

// RS Rating calc
closeDa = request.security(syminfo.tickerid, 'D', close)
spxCloseDa = request.security(comparativeTickerId, 'D', close)

perfTicker63 = nz(closeDa / closeDa[n63], 1.0)
perfTicker126 = nz(closeDa / closeDa[n126], 1.0)
perfTicker189 = nz(closeDa / closeDa[n189], 1.0)
perfTicker252 = nz(closeDa / closeDa[n252], 1.0)

perfComp63 = nz(spxCloseDa / spxCloseDa[n63], 1.0)
perfComp126 = nz(spxCloseDa / spxCloseDa[n126], 1.0)
perfComp189 = nz(spxCloseDa / spxCloseDa[n189], 1.0)
perfComp252 = nz(spxCloseDa / spxCloseDa[n252], 1.0)

float rs_stock = 0.4 * perfTicker63 + 0.2 * perfTicker126 + 0.2 * perfTicker189 + 0.2 * perfTicker252
float rs_ref = 0.4 * perfComp63 + 0.2 * perfComp126 + 0.2 * perfComp189 + 0.2 * perfComp252

float totalRsScore = rs_stock / rs_ref * 100
if na(totalRsScore)
    totalRsScore := svth
float totalRsRating = na

// RS Rating thresholds — uses fixed values from inputs (screener-safe)
// request.seed() is NOT compatible with Pine Screener, so thresholds
// are provided as inputs. Update the input values periodically from
// the RSRATING seed ticker on TradingView to keep ratings accurate.

if totalRsScore >= first
    totalRsRating := 99
if totalRsScore <= svth
    totalRsRating := 1

f_attributePercentile(totalRsScore, tallerPerf, smallerPerf, rangeUp, rangeDn, weight) =>
    sum = totalRsScore + (totalRsScore - smallerPerf) * weight
    if sum > tallerPerf - 1
        sum := tallerPerf - 1
    k1 = smallerPerf / rangeDn
    k2 = (tallerPerf - 1) / rangeUp
    k3 = (k1 - k2) / (tallerPerf - 1 - smallerPerf)
    RsRating = sum / (k1 - k3 * (totalRsScore - smallerPerf))
    if RsRating > rangeUp
        RsRating := rangeUp
    if RsRating < rangeDn
        RsRating := rangeDn
    RsRating

if totalRsScore < first and totalRsScore >= scnd
    totalRsRating := f_attributePercentile(totalRsScore, first, scnd, 98, 90, 0.33)
if totalRsScore < scnd and totalRsScore >= thrd
    totalRsRating := f_attributePercentile(totalRsScore, scnd, thrd, 89, 70, 2.1)
if totalRsScore < thrd and totalRsScore >= frth
    totalRsRating := f_attributePercentile(totalRsScore, thrd, frth, 69, 50, 0)
if totalRsScore < frth and totalRsScore >= ffth
    totalRsRating := f_attributePercentile(totalRsScore, frth, ffth, 49, 30, 0)
if totalRsScore < ffth and totalRsScore >= sxth
    totalRsRating := f_attributePercentile(totalRsScore, ffth, sxth, 29, 10, 0)
if totalRsScore < sxth and totalRsScore >= svth
    totalRsRating := f_attributePercentile(totalRsScore, sxth, svth, 9, 2, 0)

isDaily = timeframe.isdaily
labelText1 = ' RS Rating'
labelText2 = ''

if isDaily and not na(totalRsRating)
    labelText2 := '\n\n ' + str.tostring(totalRsRating, '#0')

if ratingOnly and not na(totalRsRating)
    labelText1 := ''
    labelText2 := '\n ' + str.tostring(totalRsRating, '#0')

label1 = hideRSRat == false and barstate.islast ? label.new(bar_index, rs, text=labelText1, color=color.rgb(0, 0, 0, 100), size=size.normal, textcolor=colorRS, style=label.style_label_center, textalign=text.align_left, yloc=yloc.price) : na
label2 = hideRSRat == false and barstate.islast ? label.new(bar_index, rs, text=labelText2, color=color.rgb(0, 0, 0, 100), size=size.large, textcolor=colorRS, style=label.style_label_center, textalign=text.align_left, yloc=yloc.price) : na

label.delete(label1[1])
label.delete(label2[1])

// ======================================================
// QULLA Base Forming (Compression)
// ======================================================

bbLen = input.int(20, 'BB Length', group='QULLA Base')
bbMult = input.float(2.0, 'BB Mult', group='QULLA Base')
atrLen = input.int(14, 'ATR Length', group='QULLA Base')
atrPctMax = input.float(5.0, 'ATR% max (US default)', step=0.1, group='QULLA Base')
volLen = input.int(20, 'Volume MA Length', group='QULLA Base')
volMax = input.float(0.85, 'Vol/VolMA max (dry)', step=0.01, group='QULLA Base')
bbWidthPctMax = input.float(7.0, 'BB Width% max (tight)', step=0.1, group='QULLA Base')

emaFastLen = input.int(10, 'Fast EMA Length', group='QULLA Base')
emaMidLen = input.int(20, 'Mid EMA Length', group='QULLA Base')
emaFast = ta.ema(close, emaFastLen)
emaMid = ta.ema(close, emaMidLen)

useAtrCompression = input.bool(true, 'Require ATR% compression vs lookback?', group='QULLA Base')
atrCmpLookback = input.int(5, 'ATR% Compression Lookback (bars)', minval=1, group='QULLA Base')
atrCmpRatio = input.float(0.90, 'ATR% must be <= prior ×', step=0.01, group='QULLA Base')

basis = ta.sma(close, bbLen)
dev = bbMult * ta.stdev(close, bbLen)
upper = basis + dev
lower = basis - dev
bbWidthPct = (upper - lower) / close * 100.0

atr = ta.atr(atrLen)
atrPct = atr / close * 100.0

atrPctPrev = atrPct[atrCmpLookback]
atrCompressionOk = not useAtrCompression ? true : not na(atrPctPrev) and atrPct <= atrPctPrev * atrCmpRatio

volMA = ta.sma(volume, volLen)
volRel = volume / volMA

baseForming = close > emaFast and emaFast > emaMid and bbWidthPct <= bbWidthPctMax and atrPct <= atrPctMax and volRel <= volMax and atrCompressionOk

plot(baseForming ? rs : na, title='QULLA BASE (RS pane)', style=plot.style_circles, linewidth=3, color=color.lime)
plot(emaFast, title='EMA 10 (hidden)', display=display.none)
plot(emaMid, title='EMA 20 (hidden)', display=display.none)

alertcondition(baseForming, title='QULLA_BASE_FORMING', message='QULLA_BASE_FORMING')

// ======================================================
// PINE SCREENER — RS Rating Filtering
// ======================================================
// HOW TO USE IN PINE SCREENER:
//   1. Open TradingView → Stock Screener → Pine Screener tab
//   2. Add this indicator as a filter
//   3. To find RS 99 stocks: filter "RS Rating Is 99" crosses above 0.5
//   4. To filter by RS number: set Min/Max RS Rating inputs, then
//      filter "RS Rating In Range" crosses above 0.5
//   5. To find any stock with RS above a threshold: filter "RS Rating" > 80
//   NOTE: RS Rating uses approximate data for stocks with <12 months history

screenRSMin = input.int(99, title='Min RS Rating', minval=0, maxval=99, group='Screen', tooltip='Minimum RS Rating for range filter. Set 99 to find RS 99 stocks.')
screenRSMax = input.int(99, title='Max RS Rating', minval=1, maxval=99, group='Screen', tooltip='Maximum RS Rating for range filter. Set 99 to find RS 99 stocks.')

// Plot RS Rating as numeric value; na when data unavailable (screener ignores na)
validRS = not na(totalRsRating)
plot(validRS ? totalRsRating : na, title='RS Rating', display=display.data_window)

// Boolean: RS Rating equals 99 (quick filter for top-rated stocks)
isRS99 = validRS and totalRsRating == 99
plot(isRS99 ? 1 : 0, title='RS Rating Is 99', display=display.data_window)

// Boolean: RS Rating within user-defined min/max range
isRSInRange = validRS and totalRsRating >= screenRSMin and totalRsRating <= screenRSMax
plot(isRSInRange ? 1 : 0, title='RS Rating In Range', display=display.data_window)

// Alert conditions
alertcondition(isRS99, title='RS Rating 99', message='{{ticker}} has RS Rating 99')
alertcondition(isRSInRange, title='RS Rating In Range', message='{{ticker}} RS Rating {{plot_0}} is in range')
```

Original source (v5, pre-screener): https://www.tradingview.com/script/pziQwiT2/

#tradingview #indicator

---


#tradingview #indicator
