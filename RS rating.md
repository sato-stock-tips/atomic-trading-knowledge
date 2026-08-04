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