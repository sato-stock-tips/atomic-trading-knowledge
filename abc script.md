//@version=6
// abc Script v3.1 (Aug 2026)
//
// WHAT WAS BROKEN in v2:
//   The Donchian breakout used ta.crossover(close, upperDonch) where upperDonch
//   INCLUDED the current candle's high. Close can never exceed the highest high
//   of a window that contains its own candle, so the plot was 0 on every bar and
//   no screener condition (">0", "=1", "crossing 0.5") could ever match.
//
// FIX in v3:
//   Breakout is now measured against the PRIOR 55-candle high / PRIOR 20-candle
//   low (channel shifted back one bar). The two screener plots output the COUNT
//   of breakout candles within the last 3 candles (0..3), so in Pine Screener you
//   just set the condition to "greater than 0" and every stock that broke out of
//   the relevant Donchian channel within the past 3 candles is displayed.
//
// ADDED in v3.1 (Aug 2026):
//   New standalone "RS Rating (numeric 1-99) [8 weeks]" column. It runs the SAME
//   1-99 percentile-bucket mapping as the original RS Rating, but the input score
//   is a PURE 8-week (40 trading day) stock-vs-SPX ratio instead of the 40/20/20/20
//   quarterly blend — so it only measures the past 8 weeks, nothing longer.
//   It uses its OWN threshold inputs (grpRS8W) because the 8-week score has a much
//   tighter distribution than the blended one; the original thresholds (195.93,
//   117.11, ...) were calibrated for the wide 252-day blend and would misclassify
//   almost everything if reused here. The seeded defaults below are a first-guess
//   sqrt-time scaling (~0.40x) of the original thresholds around the 100 midpoint —
//   NOT a backtested calibration. Recalibrate by eye once you have screener data:
//   widen the thresholds if too many names cluster at 99, narrow them if almost
//   nothing reaches 99/90.
//
// PLOT ORDER (Pine Screener lists plots in declaration order — first 7 on top):
//   1. RS Rating (numeric 1-99)
//   2. RS Rating (numeric 1-99) [8 weeks]
//   3. RS Rating Is 99
//   4. 3+ Long Signals in Last 12 Candles
//   5. 3+ Short Signals in Last 12 Candles
//   6. Donchian 55 Upper Breakout (last 3 candles)   -> screen with "> 0"
//   7. Donchian 20 Lower Breakdown (last 3 candles)  -> screen with "> 0"
//   Everything else (extra Surf/Turf plots, chart lines, Donchian Midline)
//   is declared AFTER these seven, so it lands behind the dropdown.

indicator(title='abc v3.1', shorttitle='abc v3.1', overlay=true, max_bars_back=500)

// ==============================================================================
// INPUT GROUPS
// ==============================================================================

grpRS = 'RS Rating Thresholds'
first = input.float(195.93, 'RS Score for 99+ rating', group=grpRS)
scnd  = input.float(117.11, 'RS Score for 90+ rating', group=grpRS)
thrd  = input.float(99.04,  'RS Score for 70+ rating', group=grpRS)
frth  = input.float(91.66,  'RS Score for 50+ rating', group=grpRS)
ffth  = input.float(80.96,  'RS Score for 30+ rating', group=grpRS)
sxth  = input.float(53.64,  'RS Score for 10+ rating', group=grpRS)
svth  = input.float(24.86,  'RS Score for 1+ rating',  group=grpRS)
showRSLabel = input.bool(false, 'Show current RS Rating label on chart', group=grpRS)

grpRS8W = 'RS Rating Thresholds [8 weeks]'
first8w = input.float(138.37, 'RS Score for 99+ rating', group=grpRS8W)
scnd8w  = input.float(106.84, 'RS Score for 90+ rating', group=grpRS8W)
thrd8w  = input.float(99.62,  'RS Score for 70+ rating', group=grpRS8W)
frth8w  = input.float(96.66,  'RS Score for 50+ rating', group=grpRS8W)
ffth8w  = input.float(92.38,  'RS Score for 30+ rating', group=grpRS8W)
sxth8w  = input.float(81.46,  'RS Score for 10+ rating', group=grpRS8W)
svth8w  = input.float(69.94,  'RS Score for 1+ rating',  group=grpRS8W)

grpMA   = 'SurfTurf v4 | Moving Averages'
ema10Length = input.int(10, 'EMA 10 Length', minval=1, group=grpMA)
ema20Length = input.int(20, 'EMA 20 Length', minval=1, group=grpMA)
sma50Length = input.int(50, 'SMA 50 Length', minval=1, group=grpMA)

grpATR  = 'SurfTurf v4 | ATR Settings'
atrLength            = input.int(14, 'ATR Length', minval=1, group=grpATR)
maxATRMultipleFrom50 = input.float(4.0, 'Max ATR% Multiple from 50 SMA', minval=1.0, maxval=10.0, step=0.5, group=grpATR)
pullbackATRThreshold = input.float(1.5, 'Pullback ATR Threshold', minval=0.5, maxval=3.0, step=0.1, group=grpATR)

grpVol  = 'SurfTurf v4 | Volume Settings'
volLookback       = input.int(20, 'Volume SMA Lookback', minval=5, maxval=50, group=grpVol)
volDryUpThreshold = input.float(0.8, 'Volume Dry-Up Threshold', minval=0.3, maxval=1.2, step=0.05, group=grpVol)
volDryUpDays      = input.int(2, 'Min Dry-Up Days', minval=1, maxval=5, group=grpVol)

grpSurf = 'SurfTurf v4 | Surf Trade (Long)'
enableSurf          = input.bool(true, 'Enable Surf Trade Signals', group=grpSurf)
surfColor           = input.color(color.teal, 'Surf Signal Color', group=grpSurf)
requireUptrend      = input.bool(true, 'Require Uptrend (EMA10 > EMA20 > SMA50)', group=grpSurf)
requirePriceAboveMAs = input.bool(true, 'Require Price Above Key MAs', group=grpSurf)

grpTurf = 'SurfTurf v4 | Turf Trade (Long)'
enableTurf           = input.bool(true, 'Enable Turf Trade Signals', group=grpTurf)
turfColor            = input.color(color.purple, 'Turf Signal Color', group=grpTurf)
maClumpThreshold     = input.float(3.0, 'MA Clump Threshold (%)', minval=0.5, maxval=10.0, step=0.5, group=grpTurf)
reclaimLookback      = input.int(2, 'Reclaim Lookback (sessions)', minval=1, maxval=5, group=grpTurf)
turfReclaimVolThreshold = input.float(1.0, 'Min Reclaim Volume (x avg)', minval=0.5, maxval=3.0, step=0.1, group=grpTurf)

grpInvSurf = 'SurfTurf v4 | Inverse Surf (Short)'
enableInverseSurf   = input.bool(true, 'Enable Inverse Surf Trade Signals', group=grpInvSurf)
inverseSurfColor    = input.color(color.new(color.teal, 50), 'Inverse Surf Signal Color', group=grpInvSurf)
requireDowntrend    = input.bool(true, 'Require Downtrend', group=grpInvSurf)
requirePriceBelowMAs = input.bool(true, 'Require Price Below Key MAs', group=grpInvSurf)

grpInvTurf = 'SurfTurf v4 | Inverse Turf (Short)'
enableInverseTurf = input.bool(true, 'Enable Inverse Turf Trade Signals', group=grpInvTurf)
inverseTurfColor  = input.color(color.new(color.purple, 50), 'Inverse Turf Signal Color', group=grpInvTurf)

grpDisplay = 'SurfTurf v4 | Signal Display'
dotOffset = input.float(0.5, 'Dot Offset (ATR multiple)', minval=0.1, maxval=2.0, step=0.1, group=grpDisplay)

grpDC = 'Donchian Channel (Upper 55 / Lower 20)'
showDonchian   = input.bool(true, 'Show Donchian Channels on Chart', group=grpDC)
dcUpperLen     = input.int(55, 'Upper Channel Length', minval=10, group=grpDC)
dcLowerLen     = input.int(20, 'Lower Channel Length', minval=5, group=grpDC)
dcBreakLookback = input.int(3, 'Breakout Lookback (candles)', minval=1, maxval=10, group=grpDC)
dcUseClose     = input.bool(false, 'Use Close for Breakout (off = wick/high counts)', group=grpDC)
dcUpColor      = input.color(color.blue, 'Upper Band Color', group=grpDC, inline='dccol')
dcLoColor      = input.color(color.red, 'Lower Band Color', group=grpDC, inline='dccol')
dcFillColor    = input.color(color.new(color.teal, 88), 'Channel Fill Color', group=grpDC)

// ==============================================================================
// RS RATING CALCULATION
// ==============================================================================

comparativeTickerId = 'SP:SPX'

n63  = bar_index < 63  ? bar_index : 63
n126 = bar_index < 126 ? bar_index : 126
n189 = bar_index < 189 ? bar_index : 189
n252 = bar_index < 252 ? bar_index : 252

closeDa    = request.security(syminfo.tickerid, 'D', close)
spxCloseDa = request.security(comparativeTickerId, 'D', close)

perfTicker63  = nz(closeDa    / closeDa[n63],  1.0)
perfTicker126 = nz(closeDa    / closeDa[n126], 1.0)
perfTicker189 = nz(closeDa    / closeDa[n189], 1.0)
perfTicker252 = nz(closeDa    / closeDa[n252], 1.0)

perfComp63  = nz(spxCloseDa / spxCloseDa[n63],  1.0)
perfComp126 = nz(spxCloseDa / spxCloseDa[n126], 1.0)
perfComp189 = nz(spxCloseDa / spxCloseDa[n189], 1.0)
perfComp252 = nz(spxCloseDa / spxCloseDa[n252], 1.0)

float rs_stock = 0.4 * perfTicker63 + 0.2 * perfTicker126 + 0.2 * perfTicker189 + 0.2 * perfTicker252
float rs_ref   = 0.4 * perfComp63  + 0.2 * perfComp126  + 0.2 * perfComp189  + 0.2 * perfComp252

float totalRsScore = rs_stock / rs_ref * 100
if na(totalRsScore)
    totalRsScore := svth

float totalRsRating = na

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

if totalRsScore >= first
    totalRsRating := 99
if totalRsScore <= svth
    totalRsRating := 1
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

validRS = not na(totalRsRating)
isRS99  = validRS and totalRsRating == 99

// ------------------------------------------------------------------------------
// RS RATING [8 WEEKS] — same 1-99 bucket mapping, but the score is a pure
// 40-trading-day (8 week) stock-vs-SPX ratio, not the 40/20/20/20 quarter blend.
// ------------------------------------------------------------------------------

n40 = bar_index < 40 ? bar_index : 40

perfTicker8w = nz(closeDa    / closeDa[n40],    1.0)
perfComp8w   = nz(spxCloseDa / spxCloseDa[n40], 1.0)

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

validRS8w = not na(totalRsRating8w)

// ==============================================================================
// SURF & TURF v4 LOGIC
// ==============================================================================

ema10 = ta.ema(close, ema10Length)
ema20 = ta.ema(close, ema20Length)
sma50 = ta.sma(close, sma50Length)

atr = ta.atr(atrLength)
atrPercent = atr / close * 100

distanceFrom50 = close - sma50
distanceFrom50Pct = distanceFrom50 / sma50 * 100
atrMultipleFrom50 = atrPercent != 0 ? distanceFrom50Pct / atrPercent : 0

volSMA = ta.sma(volume, volLookback)
relativeVol = volSMA != 0 ? volume / volSMA : 1

dryUpCount = int(0)
dryUpCount := volume < volSMA * volDryUpThreshold ? dryUpCount + 1 : dryUpCount
dryUpCount := volume[1] < volSMA[1] * volDryUpThreshold ? dryUpCount + 1 : dryUpCount
dryUpCount := volDryUpDays > 2 and volume[2] < volSMA[2] * volDryUpThreshold ? dryUpCount + 1 : dryUpCount
dryUpCount := volDryUpDays > 3 and volume[3] < volSMA[3] * volDryUpThreshold ? dryUpCount + 1 : dryUpCount
dryUpCount := volDryUpDays > 4 and volume[4] < volSMA[4] * volDryUpThreshold ? dryUpCount + 1 : dryUpCount

hasVolumeDryUp = dryUpCount >= volDryUpDays

isUptrend   = ema10 > ema20 and ema20 > sma50
isDowntrend = ema10 < ema20 and ema20 < sma50

// SURF LONG
priceAboveMAs = close > ema10 and close > ema20 and close > sma50
notExtendedLong = atrMultipleFrom50 < maxATRMultipleFrom50 and atrMultipleFrom50 > 0

distToEMA10 = math.abs(low - ema10)
distToEMA20 = math.abs(low - ema20)
atrThresholdValue = atr * pullbackATRThreshold

touchingEMA10Long = distToEMA10 <= atrThresholdValue and low <= ema10 * 1.02
touchingEMA20Long = distToEMA20 <= atrThresholdValue and low <= ema20 * 1.02
isPullbackToMA = touchingEMA10Long or touchingEMA20Long

recentPullback = close[1] < close[2] or close[2] < close[3]
closeNearHigh = close - low > (high - low) * 0.5
bounceFromMA = close > open and low <= math.max(ema10, ema20) * 1.01

isPullbackEnding = (recentPullback and (closeNearHigh or bounceFromMA)) or (low <= ema20 and close > ema20 and close > open)

surfVolCondition = hasVolumeDryUp
surfUptrendCondition = requireUptrend ? isUptrend : true
surfPriceCondition   = requirePriceAboveMAs ? close > ema20 and close > sma50 : true

surfSignal = enableSurf and surfUptrendCondition and surfPriceCondition and notExtendedLong and isPullbackToMA and isPullbackEnding and surfVolCondition

// TURF LONG
highestMA = math.max(math.max(ema10, ema20), sma50)
lowestMA  = math.min(math.min(ema10, ema20), sma50)
maSpread  = (highestMA - lowestMA) / lowestMA * 100
masAreClumped = maSpread <= maClumpThreshold

turfTrendAligned = isUptrend

consolidationDryUpCount = int(0)
consolidationDryUpCount := volume[2] < volSMA[2] * volDryUpThreshold ? consolidationDryUpCount + 1 : consolidationDryUpCount
consolidationDryUpCount := volume[3] < volSMA[3] * volDryUpThreshold ? consolidationDryUpCount + 1 : consolidationDryUpCount
consolidationDryUpCount := volume[4] < volSMA[4] * volDryUpThreshold ? consolidationDryUpCount + 1 : consolidationDryUpCount
consolidationDryUpCount := volume[5] < volSMA[5] * volDryUpThreshold ? consolidationDryUpCount + 1 : consolidationDryUpCount
consolidationDryUpCount := volume[6] < volSMA[6] * volDryUpThreshold ? consolidationDryUpCount + 1 : consolidationDryUpCount
consolidationDryUpCount := volume[7] < volSMA[7] * volDryUpThreshold ? consolidationDryUpCount + 1 : consolidationDryUpCount
consolidationDryUpCount := volume[8] < volSMA[8] * volDryUpThreshold ? consolidationDryUpCount + 1 : consolidationDryUpCount
consolidationDryUpCount := volume[9] < volSMA[9] * volDryUpThreshold ? consolidationDryUpCount + 1 : consolidationDryUpCount
consolidationDryUpCount := volume[10] < volSMA[10] * volDryUpThreshold ? consolidationDryUpCount + 1 : consolidationDryUpCount

hasConsolidationDryUp = consolidationDryUpCount >= volDryUpDays
reclaimVolume = relativeVol > turfReclaimVolThreshold
turfVolumeCondition = hasConsolidationDryUp and reclaimVolume

priceAboveAllMAs = close > ema10 and close > ema20 and close > sma50

priceWasBelowMA = close[1] < ema10[1] or close[1] < ema20[1] or close[1] < sma50[1] or 
                  (reclaimLookback > 1 and (close[2] < ema10[2] or close[2] < ema20[2] or close[2] < sma50[2])) or
                  (reclaimLookback > 2 and (close[3] < ema10[3] or close[3] < ema20[3] or close[3] < sma50[3])) or
                  (reclaimLookback > 3 and (close[4] < ema10[4] or close[4] < ema20[4] or close[4] < sma50[4])) or
                  (reclaimLookback > 4 and (close[5] < ema10[5] or close[5] < ema20[5] or close[5] < sma50[5]))

reclaimWithConviction = close > open and close - low > (high - low) * 0.4

emaCrossover = ta.crossover(ema10, ema20)
emaCrossoverRecent = emaCrossover or emaCrossover[1] or emaCrossover[2]
emasConverging = math.abs(ema10 - ema20) / ema20 * 100 < 1.0

turfSignal = enableTurf and turfTrendAligned and masAreClumped and turfVolumeCondition and priceAboveAllMAs and priceWasBelowMA and reclaimWithConviction and (emaCrossoverRecent or emasConverging)

// INVERSE SURF SHORT
priceBelowMAs = close < ema10 and close < ema20 and close < sma50
notCollapsedShort = atrMultipleFrom50 > -maxATRMultipleFrom50 and atrMultipleFrom50 < 0

distHighToEMA10 = math.abs(high - ema10)
distHighToEMA20 = math.abs(high - ema20)

touchingEMA10Short = distHighToEMA10 <= atrThresholdValue and high >= ema10 * 0.98
touchingEMA20Short = distHighToEMA20 <= atrThresholdValue and high >= ema20 * 0.98
isRallyToMA = touchingEMA10Short or touchingEMA20Short

recentRally = close[1] > close[2] or close[2] > close[3]
closeNearLow = high - close > (high - low) * 0.5
rejectionFromMA = close < open and high >= math.min(ema10, ema20) * 0.99

isRallyEnding = (recentRally and (closeNearLow or rejectionFromMA)) or (high >= ema20 and close < ema20 and close < open)

inverseSurfVolCondition = hasVolumeDryUp

inverseSurfDowntrendCondition = requireDowntrend ? isDowntrend : true
inverseSurfPriceCondition   = requirePriceBelowMAs ? close < ema20 and close < sma50 : true

inverseSurfSignal = enableInverseSurf and inverseSurfDowntrendCondition and inverseSurfPriceCondition and notCollapsedShort and isRallyToMA and isRallyEnding and inverseSurfVolCondition

// INVERSE TURF SHORT
inverseTurfTrendAligned = isDowntrend
inverseTurfVolumeCondition = hasConsolidationDryUp and reclaimVolume

priceBelowAllMAs = close < ema10 and close < ema20 and close < sma50

priceWasAboveMA = close[1] > ema10[1] or close[1] > ema20[1] or close[1] > sma50[1] or 
                  (reclaimLookback > 1 and (close[2] > ema10[2] or close[2] > ema20[2] or close[2] > sma50[2])) or
                  (reclaimLookback > 2 and (close[3] > ema10[3] or close[3] > ema20[3] or close[3] > sma50[3])) or
                  (reclaimLookback > 3 and (close[4] > ema10[4] or close[4] > ema20[4] or close[4] > sma50[4])) or
                  (reclaimLookback > 4 and (close[5] > ema10[5] or close[5] > ema20[5] or close[5] > sma50[5]))

breakdownWithConviction = close < open and high - close > (high - low) * 0.4

emaCrossunder = ta.crossunder(ema10, ema20)
emaCrossunderRecent = emaCrossunder or emaCrossunder[1] or emaCrossunder[2]

inverseTurfSignal = enableInverseTurf and inverseTurfTrendAligned and masAreClumped and inverseTurfVolumeCondition and priceBelowAllMAs and priceWasAboveMA and breakdownWithConviction and (emaCrossunderRecent or emasConverging)

// 12-candle signal counts
anyLongSignal  = surfSignal or turfSignal
anyShortSignal = inverseSurfSignal or inverseTurfSignal

longSignalCount12  = math.sum(anyLongSignal ? 1 : 0, 12)
shortSignalCount12 = math.sum(anyShortSignal ? 1 : 0, 12)

hasMin3Long  = longSignalCount12 >= 3
hasMin3Short = shortSignalCount12 >= 3

// ==============================================================================
// DONCHIAN CHANNEL — FIXED BREAKOUT LOGIC
// ==============================================================================
// The channel used for BREAKOUT DETECTION excludes the current candle ([1]),
// otherwise price can never exceed its own high and the condition never fires.

upperDonch = ta.highest(high, dcUpperLen)   // for chart display
lowerDonch = ta.lowest(low, dcLowerLen)     // for chart display
midDonch   = (upperDonch + lowerDonch) / 2  // for chart display

priorUpper55 = upperDonch[1]  // highest high of the PREVIOUS 55 candles
priorLower20 = lowerDonch[1]  // lowest low of the PREVIOUS 20 candles

breakUpSrc = dcUseClose ? close : high
breakDnSrc = dcUseClose ? close : low

dcBreakUpBar = breakUpSrc > priorUpper55   // this candle broke above the 55-high
dcBreakDnBar = breakDnSrc < priorLower20   // this candle broke below the 20-low

// Count of breakout candles within the last N (default 3) candles: 0..N.
// In Pine Screener set the condition to "greater than 0".
dcBreakUpLast3 = math.sum(dcBreakUpBar ? 1 : 0, dcBreakLookback)
dcBreakDnLast3 = math.sum(dcBreakDnBar ? 1 : 0, dcBreakLookback)

// ==============================================================================
// PINE SCREENER OUTPUTS — THE 7 PRIMARY FILTERS (declared first = shown first)
// ==============================================================================

plot(validRS ? totalRsRating : na, title='1. RS Rating (numeric 1-99)', display=display.data_window)
plot(validRS8w ? totalRsRating8w : na, title='2. RS Rating (numeric 1-99) [8 weeks]', display=display.data_window)
plot(isRS99 ? 1 : 0, title='3. RS Rating Is 99', display=display.data_window)
plot(hasMin3Long ? 1 : 0, title='4. 3+ Long Signals in Last 12 Candles', display=display.data_window)
plot(hasMin3Short ? 1 : 0, title='5. 3+ Short Signals in Last 12 Candles', display=display.data_window)
plot(dcBreakUpLast3, title='6. Donchian 55 Upper Breakout (last 3 candles)', display=display.data_window)
plot(dcBreakDnLast3, title='7. Donchian 20 Lower Breakdown (last 3 candles)', display=display.data_window)

// ==============================================================================
// SECONDARY SCREENER OUTPUTS (behind the dropdown)
// ==============================================================================

plot(anyLongSignal ? 1 : 0, title='Any Long Signal (Surf or Turf)', display=display.data_window)
plot(anyShortSignal ? 1 : 0, title='Any Short Signal (Inv Surf or Turf)', display=display.data_window)
plot((surfSignal and turfSignal) ? 1 : 0, title='Both Long (Surf + Turf same bar)', display=display.data_window)
plot((inverseSurfSignal and inverseTurfSignal) ? 1 : 0, title='Both Short (InvSurf + InvTurf)', display=display.data_window)
plot(longSignalCount12, title='Long Signal Count (last 12 candles)', display=display.data_window)
plot(shortSignalCount12, title='Short Signal Count (last 12 candles)', display=display.data_window)
plot(atrMultipleFrom50, title='ATR Multiple from 50 SMA', display=display.data_window)
plot(maSpread, title='MA Spread % (for Turf clump)', display=display.data_window)
plot(close > priorUpper55 ? 1 : 0, title='Close Currently Above Prior Donchian 55 High', display=display.data_window)
plot(close < priorLower20 ? 1 : 0, title='Close Currently Below Prior Donchian 20 Low', display=display.data_window)

// ==============================================================================
// CHART DISPLAY (declared last so chart lines sit at the back of the screener list)
// ==============================================================================

pUpper = plot(showDonchian ? upperDonch : na, color=dcUpColor, linewidth=2, title='Donchian Upper Line (chart)')
pLower = plot(showDonchian ? lowerDonch : na, color=dcLoColor, linewidth=2, title='Donchian Lower Line (chart)')
fill(pUpper, pLower, color=dcFillColor, title='Donchian Channel Fill')
plot(showDonchian ? midDonch : na, color=color.new(color.gray, 40), linewidth=1, title='Donchian Midline (chart)')

dotPositionBelow = low - atr * dotOffset
dotPositionAbove = high + atr * dotOffset

plotshape(turfSignal and not surfSignal ? dotPositionBelow : na, title='Turf Signal (Long)', location=location.absolute, style=shape.circle, size=size.small, color=turfColor)
plotshape(surfSignal and not turfSignal ? dotPositionBelow : na, title='Surf Signal (Long)', location=location.absolute, style=shape.circle, size=size.small, color=surfColor)
plotshape(surfSignal and turfSignal ? dotPositionBelow : na, title='Both Long Signals', location=location.absolute, style=shape.circle, size=size.small, color=color.yellow)

plotshape(inverseTurfSignal and not inverseSurfSignal ? dotPositionAbove : na, title='Inverse Turf Signal (Short)', location=location.absolute, style=shape.circle, size=size.small, color=inverseTurfColor)
plotshape(inverseSurfSignal and not inverseTurfSignal ? dotPositionAbove : na, title='Inverse Surf Signal (Short)', location=location.absolute, style=shape.circle, size=size.small, color=inverseSurfColor)
plotshape(inverseSurfSignal and inverseTurfSignal ? dotPositionAbove : na, title='Both Short Signals', location=location.absolute, style=shape.circle, size=size.small, color=color.orange)

var label rsLabel = na
if barstate.islast
    label.delete(rsLabel)
    if showRSLabel and validRS
        rsLabel := label.new(bar_index, close * 1.015, text='RS ' + str.tostring(totalRsRating, '#0'), 
                             color=color.rgb(0, 0, 0, 75), style=label.style_label_left, 
                             textcolor=color.new(color.blue, 0), size=size.normal, yloc=yloc.price)

// ==============================================================================
// ALERTS
// ==============================================================================

alertcondition(isRS99, title='RS Rating 99', message='{{ticker}} has RS Rating 99')

alertcondition(dcBreakUpLast3 > 0, title='Donchian 55 Upper Breakout (last 3 candles)', message='{{ticker}} broke ABOVE the prior 55-candle high within the last 3 candles')
alertcondition(dcBreakDnLast3 > 0, title='Donchian 20 Lower Breakdown (last 3 candles)', message='{{ticker}} broke BELOW the prior 20-candle low within the last 3 candles')

alertcondition(surfSignal, title='Surf Long Entry Signal', message='Surf Trade Entry on {{ticker}}')
alertcondition(turfSignal, title='Turf Long Entry Signal', message='Turf Trade Entry on {{ticker}}')
alertcondition(inverseSurfSignal, title='Inverse Surf Short Entry', message='Inverse Surf Short Signal on {{ticker}}')
alertcondition(inverseTurfSignal, title='Inverse Turf Short Entry', message='Inverse Turf Breakdown Short on {{ticker}}')

alertcondition(hasMin3Long and not hasMin3Long[1], title='3+ Long Signals in 12 Candles', message='{{ticker}} now has 3+ Surf/Turf long signals in last 12 candles')
alertcondition(hasMin3Short and not hasMin3Short[1], title='3+ Short Signals in 12 Candles', message='{{ticker}} now has 3+ Surf/Turf short signals in last 12 candles')

// ==============================================================================
// END OF abc v3.1
// ==============================================================================
