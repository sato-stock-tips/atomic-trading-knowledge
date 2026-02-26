# Surf & Turf Entry Signals (Pine Script)

TradingView indicator for identifying [[surf trade]] and [[turf trade]] setups, including inverse (short-side) versions.

## Overview

This indicator displays entry signals as colored dots:
- **Long signals**: Dots BELOW candles
- **Short signals**: Dots ABOVE candles

### Signal Colors

| Signal               | Color          | Position     |     |
| -------------------- | -------------- | ------------ | --- |
| Surf (Long)          | Teal           | Below candle |     |
| Turf (Long)          | Purple         | Below candle |     |
| Both Long            | Yellow         | Below candle |     |
| Inverse Surf (Short) | Lighter Teal   | Above candle |     |
| Inverse Turf (Short) | Lighter Purple | Above candle |     |
| Both Short           | Orange         | Above candle |     |

---

## Surf Trade Conditions (Long)

Based on [[surf trade]] - pullback to [[mark minervini (key moving averages)]].

1. **Uptrend**: EMA10 > EMA20 > SMA50
2. **Not extended**: Less than 4x [[ATR%]] from 50 SMA (see [[extended stocks]])
3. **Pullback to MA**: Price within 1.5 ATR of EMA10 or EMA20
4. **Pullback ending**: Recent down days + close near high OR bounce candle
5. **Volume dry-up**: Low [[relative volume at time]] during pullback

---

## Turf Trade Conditions (Long)

Based on [[turf trade]] - price reclaiming clumped MAs.

1. **MAs clumped**: Spread between highest and lowest MA < 3%
2. **Price above all MAs**: Close > EMA10, EMA20, and SMA50
3. **Recent reclaim**: Price was below at least one MA in past 2 sessions
4. **Conviction**: Close > open, close in upper 40% of range
5. **EMA crossover**: EMA10 crossing above EMA20 (or converging within 1%)

---

## Inverse Surf Trade Conditions (Short)

Mirror of surf trade for shorting - rally to resistance MA in downtrend.

1. **Downtrend**: EMA10 < EMA20 < SMA50
2. **Not collapsed**: Price within 4x ATR% BELOW 50 SMA (avoid shorting crashed stocks)
3. **Rally to MA**: High within 1.5 ATR of EMA10 or EMA20
4. **Rally ending**: Recent up days + close near low OR rejection candle
5. **Volume dry-up**: Low [[relative volume at time]] during rally (weak rally)

---

## Inverse Turf Trade Conditions (Short)

Mirror of turf trade for shorting - price breaking below clumped MAs.

1. **MAs clumped**: Spread between highest and lowest MA < 3%
2. **Price below all MAs**: Close < EMA10, EMA20, and SMA50
3. **Recent breakdown**: Price was above at least one MA in past 2 sessions
4. **Conviction**: Close < open, close in lower 40% of range
5. **EMA crossunder**: EMA10 crossing below EMA20 (or converging within 1%)

---

## Pine Screener Outputs

All outputs return 1 (true) or 0 (false) for filtering:

| Output Name | Description |
|-------------|-------------|
| Surf Signal (Long) | Long surf setup triggered |
| Turf Signal (Long) | Long turf setup triggered |
| Both Long Signals | Both long signals triggered |
| Inverse Surf Signal (Short) | Short surf setup triggered |
| Inverse Turf Signal (Short) | Short turf setup triggered |
| Both Short Signals | Both short signals triggered |
| Any Long Signal | Any long signal triggered |
| Any Short Signal | Any short signal triggered |
| Any Entry Signal | Any signal (long or short) |
| Trend Direction | -1 (down), 0 (neutral), 1 (up) |
| ATR Multiple from 50 SMA | Distance from 50 SMA in ATR% units |
| MA Spread % | Percentage spread between MAs |
| Relative Volume | Current volume / 20-day average |

---

## Pine Script Code (version 6 pine)

```
// This Pine Script is intended for educational purposes only

// Surf & Turf Entry Signals (Long & Short)

// Based on Qullamaggie's pullback (surf) and clumped MA reclaim (turf) setups

// Includes inverse setups for short-side trades

  

//@version=6

indicator('Surf & Turf Entry Signals', overlay = true, max_labels_count = 500)

  

// ============================================================================

// INPUT SETTINGS - GUI Adjustable

// ============================================================================

  

// --- Moving Average Settings ---

grpMA = 'Moving Averages'

ema10Length = input.int(10, 'EMA 10 Length', minval = 1, group = grpMA)

ema20Length = input.int(20, 'EMA 20 Length', minval = 1, group = grpMA)

sma50Length = input.int(50, 'SMA 50 Length', minval = 1, group = grpMA)

  

// --- ATR Settings ---

grpATR = 'ATR Settings'

atrLength = input.int(14, 'ATR Length', minval = 1, group = grpATR)

maxATRMultipleFrom50 = input.float(4.0, 'Max ATR% Multiple from 50 SMA (Extension Filter)', minval = 1.0, maxval = 10.0, step = 0.5, group = grpATR, tooltip = 'Surf trades require price to be less than this multiple from 50 SMA to avoid extended stocks')

pullbackATRThreshold = input.float(1.5, 'Pullback ATR Threshold (distance to EMA)', minval = 0.5, maxval = 3.0, step = 0.1, group = grpATR, tooltip = 'Price should be within this ATR multiple of the supporting EMA')

  

// --- Volume Settings ---

grpVol = 'Volume Settings'

volLookback = input.int(20, 'Volume SMA Lookback', minval = 5, maxval = 50, group = grpVol)

volDryUpThreshold = input.float(0.8, 'Volume Dry-Up Threshold (ratio to avg)', minval = 0.3, maxval = 1.2, step = 0.05, group = grpVol, tooltip = 'Volume below this ratio of average indicates dry-up')

volDryUpDays = input.int(2, 'Min Dry-Up Days', minval = 1, maxval = 5, group = grpVol, tooltip = 'Number of recent days with low volume to confirm dry-up')

  

// --- Surf Trade Settings (Long) ---

grpSurf = 'Surf Trade Settings (Long)'

enableSurf = input.bool(true, 'Enable Surf Trade Signals', group = grpSurf)

surfColor = input.color(color.teal, 'Surf Signal Color', group = grpSurf)

requireUptrend = input.bool(true, 'Require Uptrend (EMA10 > EMA20 > SMA50)', group = grpSurf)

requirePriceAboveMAs = input.bool(true, 'Require Price Above Key MAs', group = grpSurf)

  

// --- Turf Trade Settings (Long) ---

grpTurf = 'Turf Trade Settings (Long)'

enableTurf = input.bool(true, 'Enable Turf Trade Signals', group = grpTurf)

turfColor = input.color(color.purple, 'Turf Signal Color', group = grpTurf)

maClumpThreshold = input.float(3.0, 'MA Clump Threshold (%)', minval = 0.5, maxval = 10.0, step = 0.5, group = grpTurf, tooltip = 'Max % spread between highest and lowest MA to consider them clumped')

reclaimLookback = input.int(2, 'Reclaim Lookback (sessions)', minval = 1, maxval = 5, group = grpTurf, tooltip = 'Number of sessions to reclaim all MAs')

  

// --- Inverse Surf Trade Settings (Short) ---

grpInvSurf = 'Inverse Surf Trade Settings (Short)'

enableInverseSurf = input.bool(true, 'Enable Inverse Surf Trade Signals', group = grpInvSurf)

inverseSurfColor = input.color(color.new(color.teal, 50), 'Inverse Surf Signal Color (Lighter Teal)', group = grpInvSurf)

requireDowntrend = input.bool(true, 'Require Downtrend (EMA10 < EMA20 < SMA50)', group = grpInvSurf)

requirePriceBelowMAs = input.bool(true, 'Require Price Below Key MAs', group = grpInvSurf)

  

// --- Inverse Turf Trade Settings (Short) ---

grpInvTurf = 'Inverse Turf Trade Settings (Short)'

enableInverseTurf = input.bool(true, 'Enable Inverse Turf Trade Signals', group = grpInvTurf)

inverseTurfColor = input.color(color.new(color.purple, 50), 'Inverse Turf Signal Color (Lighter Purple)', group = grpInvTurf)

  

// --- Signal Display ---

grpDisplay = 'Signal Display'

dotOffset = input.float(0.5, 'Dot Offset (ATR multiple from candle)', minval = 0.1, maxval = 2.0, step = 0.1, group = grpDisplay)

  

// ============================================================================

// CALCULATIONS

// ============================================================================

  

// --- Moving Averages ---

ema10 = ta.ema(close, ema10Length)

ema20 = ta.ema(close, ema20Length)

sma50 = ta.sma(close, sma50Length)

  

// --- ATR Calculations ---

atr = ta.atr(atrLength)

atrPercent = atr / close * 100

  

// --- ATR% Multiple from 50 SMA ---

distanceFrom50 = close - sma50

distanceFrom50Pct = distanceFrom50 / sma50 * 100

atrMultipleFrom50 = atrPercent != 0 ? distanceFrom50Pct / atrPercent : 0

  

// --- Volume Analysis ---

volSMA = ta.sma(volume, volLookback)

relativeVol = volSMA != 0 ? volume / volSMA : 1

isVolDryUp = relativeVol < volDryUpThreshold

  

// Count dry-up days

dryUpCount = 0

for i = 0 to volDryUpDays - 1 by 1

if volume[i] < volSMA[i] * volDryUpThreshold

dryUpCount := dryUpCount + 1

dryUpCount

  

hasVolumeDryUp = dryUpCount >= volDryUpDays

  

// ============================================================================

// SURF TRADE CONDITIONS (LONG)

// The last daily candle during pullback before it pumps higher again

// ============================================================================

  

// 1. Uptrend condition: EMA10 > EMA20 > SMA50

isUptrend = ema10 > ema20 and ema20 > sma50

  

// 2. Price above key MAs (for trend confirmation)

priceAboveMAs = close > ema10 and close > ema20 and close > sma50

  

// 3. Not extended: less than 4x ATR% from 50 SMA (positive direction)

notExtendedLong = atrMultipleFrom50 < maxATRMultipleFrom50 and atrMultipleFrom50 > 0

  

// 4. Price pulled back to within 1-1.5 ATR of supporting EMA (EMA10 or EMA20)

distToEMA10 = math.abs(low - ema10)

distToEMA20 = math.abs(low - ema20)

atrThresholdValue = atr * pullbackATRThreshold

  

// Price is pulling back to EMA (touching or within threshold)

touchingEMA10Long = distToEMA10 <= atrThresholdValue and low <= ema10 * 1.02

touchingEMA20Long = distToEMA20 <= atrThresholdValue and low <= ema20 * 1.02

isPullbackToMA = touchingEMA10Long or touchingEMA20Long

  

// 5. Pullback characteristics: recent down/flat days followed by potential reversal

recentPullback = close[1] < close[2] or close[2] < close[3]

closeNearHigh = close - low > (high - low) * 0.5

bounceFromMA = close > open and low <= math.max(ema10, ema20) * 1.01

  

isPullbackEnding = recentPullback and (closeNearHigh or bounceFromMA) or low <= ema20 and close > ema20 and close > open

  

// 6. Volume dry-up during pullback

surfVolCondition = hasVolumeDryUp

  

// --- Combine Surf Conditions ---

surfUptrendCondition = requireUptrend ? isUptrend : true

surfPriceCondition = requirePriceAboveMAs ? close > ema20 and close > sma50 : true

  

surfSignal = enableSurf and surfUptrendCondition and surfPriceCondition and notExtendedLong and isPullbackToMA and isPullbackEnding and surfVolCondition

  

// ============================================================================

// TURF TRADE CONDITIONS (LONG)

// Price reclaiming clumped MAs after consolidation

// ============================================================================

  

// 1. MAs are clumped together (close to each other)

highestMA = math.max(math.max(ema10, ema20), sma50)

lowestMA = math.min(math.min(ema10, ema20), sma50)

maSpread = (highestMA - lowestMA) / lowestMA * 100

masAreClumped = maSpread <= maClumpThreshold

  

// 2. Price reclaims all three MAs (crosses above all in recent sessions)

priceAboveAllMAs = close > ema10 and close > ema20 and close > sma50

  

// Check if price was below at least one MA recently

priceWasBelowMA = false

for i = 1 to reclaimLookback by 1

if close[i] < ema10[i] or close[i] < ema20[i] or close[i] < sma50[i]

priceWasBelowMA := true

break

  

// 3. The reclaim happens with some conviction (close strong)

reclaimWithConviction = close > open and close - low > (high - low) * 0.4

  

// 4. EMA crossover potential (EMA10 crossing above EMA20, or about to)

emaCrossover = ta.crossover(ema10, ema20)

emaCrossoverRecent = emaCrossover or emaCrossover[1] or emaCrossover[2]

emasConverging = math.abs(ema10 - ema20) / ema20 * 100 < 1.0

  

// --- Combine Turf Conditions ---

turfSignal = enableTurf and masAreClumped and priceAboveAllMAs and priceWasBelowMA and reclaimWithConviction and (emaCrossoverRecent or emasConverging)

  

// ============================================================================

// INVERSE SURF TRADE CONDITIONS (SHORT)

// The last daily candle during rally before it dumps lower again

// ============================================================================

  

// 1. Downtrend condition: EMA10 < EMA20 < SMA50

isDowntrend = ema10 < ema20 and ema20 < sma50

  

// 2. Price below key MAs (for trend confirmation)

priceBelowMAs = close < ema10 and close < ema20 and close < sma50

  

// 3. Not collapsed: price should be within 4x ATR% BELOW the 50 SMA

notCollapsedShort = atrMultipleFrom50 > -maxATRMultipleFrom50 and atrMultipleFrom50 < 0

  

// 4. Price rallied back to within 1-1.5 ATR of resistance EMA (EMA10 or EMA20)

distHighToEMA10 = math.abs(high - ema10)

distHighToEMA20 = math.abs(high - ema20)

  

// Price is rallying to EMA (touching or within threshold from above)

touchingEMA10Short = distHighToEMA10 <= atrThresholdValue and high >= ema10 * 0.98

touchingEMA20Short = distHighToEMA20 <= atrThresholdValue and high >= ema20 * 0.98

isRallyToMA = touchingEMA10Short or touchingEMA20Short

  

// 5. Rally characteristics: recent up/flat days followed by potential reversal

recentRally = close[1] > close[2] or close[2] > close[3]

closeNearLow = high - close > (high - low) * 0.5

rejectionFromMA = close < open and high >= math.min(ema10, ema20) * 0.99

  

isRallyEnding = recentRally and (closeNearLow or rejectionFromMA) or high >= ema20 and close < ema20 and close < open

  

// 6. Volume dry-up during rally (weak rally)

inverseSurfVolCondition = hasVolumeDryUp

  

// --- Combine Inverse Surf Conditions ---

inverseSurfDowntrendCondition = requireDowntrend ? isDowntrend : true

inverseSurfPriceCondition = requirePriceBelowMAs ? close < ema20 and close < sma50 : true

  

inverseSurfSignal = enableInverseSurf and inverseSurfDowntrendCondition and inverseSurfPriceCondition and notCollapsedShort and isRallyToMA and isRallyEnding and inverseSurfVolCondition

  

// ============================================================================

// INVERSE TURF TRADE CONDITIONS (SHORT)

// Price breaking below clumped MAs after being above

// ============================================================================

  

// 1. MAs are clumped together (same as long turf - masAreClumped already calculated)

  

// 2. Price breaks below all three MAs (crosses below all in recent sessions)

priceBelowAllMAs = close < ema10 and close < ema20 and close < sma50

  

// Check if price was above at least one MA recently

priceWasAboveMA = false

for i = 1 to reclaimLookback by 1

if close[i] > ema10[i] or close[i] > ema20[i] or close[i] > sma50[i]

priceWasAboveMA := true

break

  

// 3. The breakdown happens with conviction (close weak)

breakdownWithConviction = close < open and high - close > (high - low) * 0.4

  

// 4. EMA crossunder potential (EMA10 crossing below EMA20, or about to)

emaCrossunder = ta.crossunder(ema10, ema20)

emaCrossunderRecent = emaCrossunder or emaCrossunder[1] or emaCrossunder[2]

  

// --- Combine Inverse Turf Conditions ---

inverseTurfSignal = enableInverseTurf and masAreClumped and priceBelowAllMAs and priceWasAboveMA and breakdownWithConviction and (emaCrossunderRecent or emasConverging)

  

// ============================================================================

// SIGNAL DISPLAY

// ============================================================================

  

// Calculate dot positions

dotPositionBelow = low - atr * dotOffset

dotPositionAbove = high + atr * dotOffset

  

// --- LONG SIGNALS (dots BELOW candles) ---

plotshape(turfSignal and not surfSignal ? dotPositionBelow : na, title = 'Turf Signal (Long)', location = location.absolute, style = shape.circle, size = size.small, color = turfColor)

plotshape(surfSignal and not turfSignal ? dotPositionBelow : na, title = 'Surf Signal (Long)', location = location.absolute, style = shape.circle, size = size.small, color = surfColor)

plotshape(surfSignal and turfSignal ? dotPositionBelow : na, title = 'Both Long Signals', location = location.absolute, style = shape.circle, size = size.small, color = color.yellow)

  

// --- SHORT SIGNALS (dots ABOVE candles) ---

plotshape(inverseTurfSignal and not inverseSurfSignal ? dotPositionAbove : na, title = 'Inverse Turf Signal (Short)', location = location.absolute, style = shape.circle, size = size.small, color = inverseTurfColor)

plotshape(inverseSurfSignal and not inverseTurfSignal ? dotPositionAbove : na, title = 'Inverse Surf Signal (Short)', location = location.absolute, style = shape.circle, size = size.small, color = inverseSurfColor)

plotshape(inverseSurfSignal and inverseTurfSignal ? dotPositionAbove : na, title = 'Both Short Signals', location = location.absolute, style = shape.circle, size = size.small, color = color.orange)

  

// ============================================================================

// SCREENER OUTPUTS (for Pine Screener compatibility)

// ============================================================================

  

// Boolean outputs for screener - LONG signals

plot(surfSignal ? 1 : 0, title = 'Surf Signal (Long)', display = display.data_window)

plot(turfSignal ? 1 : 0, title = 'Turf Signal (Long)', display = display.data_window)

plot(surfSignal and turfSignal ? 1 : 0, title = 'Both Long Signals', display = display.data_window)

  

// Boolean outputs for screener - SHORT signals

plot(inverseSurfSignal ? 1 : 0, title = 'Inverse Surf Signal (Short)', display = display.data_window)

plot(inverseTurfSignal ? 1 : 0, title = 'Inverse Turf Signal (Short)', display = display.data_window)

plot(inverseSurfSignal and inverseTurfSignal ? 1 : 0, title = 'Both Short Signals', display = display.data_window)

  

// Combined signal outputs

plot(surfSignal or turfSignal ? 1 : 0, title = 'Any Long Signal', display = display.data_window)

plot(inverseSurfSignal or inverseTurfSignal ? 1 : 0, title = 'Any Short Signal', display = display.data_window)

plot(surfSignal or turfSignal or inverseSurfSignal or inverseTurfSignal ? 1 : 0, title = 'Any Entry Signal', display = display.data_window)

  

// Numeric outputs for additional filtering in screener

plot(atrMultipleFrom50, title = 'ATR Multiple from 50 SMA', display = display.data_window)

plot(maSpread, title = 'MA Spread %', display = display.data_window)

plot(relativeVol, title = 'Relative Volume', display = display.data_window)

  

// Trend direction indicator (-1 = downtrend, 0 = neutral, 1 = uptrend)

trendDirection = isUptrend ? 1 : isDowntrend ? -1 : 0

plot(trendDirection, title = 'Trend Direction', display = display.data_window)

  

// ============================================================================

// ALERTS

// ============================================================================

  

// Long alerts

alertcondition(surfSignal, title = 'Surf Trade Entry (Long)', message = 'Surf Trade Signal: Pullback to MA with volume dry-up detected on {{ticker}}')

alertcondition(turfSignal, title = 'Turf Trade Entry (Long)', message = 'Turf Trade Signal: Price reclaiming clumped MAs on {{ticker}}')

alertcondition(surfSignal or turfSignal, title = 'Any Long Entry Signal', message = 'Long Entry Signal detected on {{ticker}}')

  

// Short alerts

alertcondition(inverseSurfSignal, title = 'Inverse Surf Trade Entry (Short)', message = 'Inverse Surf Signal: Rally to MA with volume dry-up detected on {{ticker}} - SHORT setup')

alertcondition(inverseTurfSignal, title = 'Inverse Turf Trade Entry (Short)', message = 'Inverse Turf Signal: Price breaking below clumped MAs on {{ticker}} - SHORT setup')

alertcondition(inverseSurfSignal or inverseTurfSignal, title = 'Any Short Entry Signal', message = 'Short Entry Signal detected on {{ticker}}')

  

// Combined alerts

alertcondition(surfSignal or turfSignal or inverseSurfSignal or inverseTurfSignal, title = 'Any Entry Signal (Long or Short)', message = 'Entry Signal detected on {{ticker}}')
```

## Pine Script Code (version 5 pine)

```pinescript
// This Pine Script is intended for educational purposes only
// Surf & Turf Entry Signals (Long & Short)
// Based on Qullamaggie's pullback (surf) and clumped MA reclaim (turf) setups
// Includes inverse setups for short-side trades

//@version=5
indicator("Surf & Turf Entry Signals", overlay=true, max_labels_count=500)

// ============================================================================
// INPUT SETTINGS - GUI Adjustable
// ============================================================================

// --- Moving Average Settings ---
grpMA = "Moving Averages"
ema10Length = input.int(10, "EMA 10 Length", minval=1, group=grpMA)
ema20Length = input.int(20, "EMA 20 Length", minval=1, group=grpMA)
sma50Length = input.int(50, "SMA 50 Length", minval=1, group=grpMA)

// --- ATR Settings ---
grpATR = "ATR Settings"
atrLength = input.int(14, "ATR Length", minval=1, group=grpATR)
maxATRMultipleFrom50 = input.float(4.0, "Max ATR% Multiple from 50 SMA (Extension Filter)", minval=1.0, maxval=10.0, step=0.5, group=grpATR, tooltip="Surf trades require price to be less than this multiple from 50 SMA to avoid extended stocks")
pullbackATRThreshold = input.float(1.5, "Pullback ATR Threshold (distance to EMA)", minval=0.5, maxval=3.0, step=0.1, group=grpATR, tooltip="Price should be within this ATR multiple of the supporting EMA")

// --- Volume Settings ---
grpVol = "Volume Settings"
volLookback = input.int(20, "Volume SMA Lookback", minval=5, maxval=50, group=grpVol)
volDryUpThreshold = input.float(0.8, "Volume Dry-Up Threshold (ratio to avg)", minval=0.3, maxval=1.2, step=0.05, group=grpVol, tooltip="Volume below this ratio of average indicates dry-up")
volDryUpDays = input.int(2, "Min Dry-Up Days", minval=1, maxval=5, group=grpVol, tooltip="Number of recent days with low volume to confirm dry-up")

// --- Surf Trade Settings (Long) ---
grpSurf = "Surf Trade Settings (Long)"
enableSurf = input.bool(true, "Enable Surf Trade Signals", group=grpSurf)
surfColor = input.color(color.teal, "Surf Signal Color", group=grpSurf)
requireUptrend = input.bool(true, "Require Uptrend (EMA10 > EMA20 > SMA50)", group=grpSurf)
requirePriceAboveMAs = input.bool(true, "Require Price Above Key MAs", group=grpSurf)

// --- Turf Trade Settings (Long) ---
grpTurf = "Turf Trade Settings (Long)"
enableTurf = input.bool(true, "Enable Turf Trade Signals", group=grpTurf)
turfColor = input.color(color.purple, "Turf Signal Color", group=grpTurf)
maClumpThreshold = input.float(3.0, "MA Clump Threshold (%)", minval=0.5, maxval=10.0, step=0.5, group=grpTurf, tooltip="Max % spread between highest and lowest MA to consider them clumped")
reclaimLookback = input.int(2, "Reclaim Lookback (sessions)", minval=1, maxval=5, group=grpTurf, tooltip="Number of sessions to reclaim all MAs")

// --- Inverse Surf Trade Settings (Short) ---
grpInvSurf = "Inverse Surf Trade Settings (Short)"
enableInverseSurf = input.bool(true, "Enable Inverse Surf Trade Signals", group=grpInvSurf)
inverseSurfColor = input.color(color.new(color.teal, 50), "Inverse Surf Signal Color (Lighter Teal)", group=grpInvSurf)
requireDowntrend = input.bool(true, "Require Downtrend (EMA10 < EMA20 < SMA50)", group=grpInvSurf)
requirePriceBelowMAs = input.bool(true, "Require Price Below Key MAs", group=grpInvSurf)

// --- Inverse Turf Trade Settings (Short) ---
grpInvTurf = "Inverse Turf Trade Settings (Short)"
enableInverseTurf = input.bool(true, "Enable Inverse Turf Trade Signals", group=grpInvTurf)
inverseTurfColor = input.color(color.new(color.purple, 50), "Inverse Turf Signal Color (Lighter Purple)", group=grpInvTurf)

// --- Signal Display ---
grpDisplay = "Signal Display"
dotOffset = input.float(0.5, "Dot Offset (ATR multiple from candle)", minval=0.1, maxval=2.0, step=0.1, group=grpDisplay)

// ============================================================================
// CALCULATIONS
// ============================================================================

// --- Moving Averages ---
ema10 = ta.ema(close, ema10Length)
ema20 = ta.ema(close, ema20Length)
sma50 = ta.sma(close, sma50Length)

// --- ATR Calculations ---
atr = ta.atr(atrLength)
atrPercent = (atr / close) * 100

// --- ATR% Multiple from 50 SMA ---
distanceFrom50 = close - sma50
distanceFrom50Pct = (distanceFrom50 / sma50) * 100
atrMultipleFrom50 = atrPercent != 0 ? distanceFrom50Pct / atrPercent : 0

// --- Volume Analysis ---
volSMA = ta.sma(volume, volLookback)
relativeVol = volSMA != 0 ? volume / volSMA : 1
isVolDryUp = relativeVol < volDryUpThreshold

// Count dry-up days
dryUpCount = 0
for i = 0 to volDryUpDays - 1
    if volume[i] < volSMA[i] * volDryUpThreshold
        dryUpCount += 1

hasVolumeDryUp = dryUpCount >= volDryUpDays

// ============================================================================
// SURF TRADE CONDITIONS (LONG)
// The last daily candle during pullback before it pumps higher again
// ============================================================================

// 1. Uptrend condition: EMA10 > EMA20 > SMA50
isUptrend = ema10 > ema20 and ema20 > sma50

// 2. Price above key MAs (for trend confirmation)
priceAboveMAs = close > ema10 and close > ema20 and close > sma50

// 3. Not extended: less than 4x ATR% from 50 SMA (positive direction)
notExtendedLong = atrMultipleFrom50 < maxATRMultipleFrom50 and atrMultipleFrom50 > 0

// 4. Price pulled back to within 1-1.5 ATR of supporting EMA (EMA10 or EMA20)
distToEMA10 = math.abs(low - ema10)
distToEMA20 = math.abs(low - ema20)
atrThresholdValue = atr * pullbackATRThreshold

// Price is pulling back to EMA (touching or within threshold)
touchingEMA10Long = distToEMA10 <= atrThresholdValue and low <= ema10 * 1.02
touchingEMA20Long = distToEMA20 <= atrThresholdValue and low <= ema20 * 1.02
isPullbackToMA = touchingEMA10Long or touchingEMA20Long

// 5. Pullback characteristics: recent down/flat days followed by potential reversal
recentPullback = close[1] < close[2] or close[2] < close[3]
closeNearHigh = (close - low) > (high - low) * 0.5
bounceFromMA = close > open and low <= math.max(ema10, ema20) * 1.01

isPullbackEnding = (recentPullback and (closeNearHigh or bounceFromMA)) or (low <= ema20 and close > ema20 and close > open)

// 6. Volume dry-up during pullback
surfVolCondition = hasVolumeDryUp

// --- Combine Surf Conditions ---
surfUptrendCondition = requireUptrend ? isUptrend : true
surfPriceCondition = requirePriceAboveMAs ? (close > ema20 and close > sma50) : true

surfSignal = enableSurf and surfUptrendCondition and surfPriceCondition and notExtendedLong and isPullbackToMA and isPullbackEnding and surfVolCondition

// ============================================================================
// TURF TRADE CONDITIONS (LONG)
// Price reclaiming clumped MAs after consolidation
// ============================================================================

// 1. MAs are clumped together (close to each other)
highestMA = math.max(math.max(ema10, ema20), sma50)
lowestMA = math.min(math.min(ema10, ema20), sma50)
maSpread = ((highestMA - lowestMA) / lowestMA) * 100
masAreClumped = maSpread <= maClumpThreshold

// 2. Price reclaims all three MAs (crosses above all in recent sessions)
priceAboveAllMAs = close > ema10 and close > ema20 and close > sma50

// Check if price was below at least one MA recently
priceWasBelowMA = false
for i = 1 to reclaimLookback
    if close[i] < ema10[i] or close[i] < ema20[i] or close[i] < sma50[i]
        priceWasBelowMA := true
        break

// 3. The reclaim happens with some conviction (close strong)
reclaimWithConviction = close > open and (close - low) > (high - low) * 0.4

// 4. EMA crossover potential (EMA10 crossing above EMA20, or about to)
emaCrossover = ta.crossover(ema10, ema20)
emaCrossoverRecent = emaCrossover or emaCrossover[1] or emaCrossover[2]
emasConverging = math.abs(ema10 - ema20) / ema20 * 100 < 1.0

// --- Combine Turf Conditions ---
turfSignal = enableTurf and masAreClumped and priceAboveAllMAs and priceWasBelowMA and reclaimWithConviction and (emaCrossoverRecent or emasConverging)

// ============================================================================
// INVERSE SURF TRADE CONDITIONS (SHORT)
// The last daily candle during rally before it dumps lower again
// ============================================================================

// 1. Downtrend condition: EMA10 < EMA20 < SMA50
isDowntrend = ema10 < ema20 and ema20 < sma50

// 2. Price below key MAs (for trend confirmation)
priceBelowMAs = close < ema10 and close < ema20 and close < sma50

// 3. Not collapsed: price should be within 4x ATR% BELOW the 50 SMA
notCollapsedShort = atrMultipleFrom50 > -maxATRMultipleFrom50 and atrMultipleFrom50 < 0

// 4. Price rallied back to within 1-1.5 ATR of resistance EMA (EMA10 or EMA20)
distHighToEMA10 = math.abs(high - ema10)
distHighToEMA20 = math.abs(high - ema20)

// Price is rallying to EMA (touching or within threshold from above)
touchingEMA10Short = distHighToEMA10 <= atrThresholdValue and high >= ema10 * 0.98
touchingEMA20Short = distHighToEMA20 <= atrThresholdValue and high >= ema20 * 0.98
isRallyToMA = touchingEMA10Short or touchingEMA20Short

// 5. Rally characteristics: recent up/flat days followed by potential reversal
recentRally = close[1] > close[2] or close[2] > close[3]
closeNearLow = (high - close) > (high - low) * 0.5
rejectionFromMA = close < open and high >= math.min(ema10, ema20) * 0.99

isRallyEnding = (recentRally and (closeNearLow or rejectionFromMA)) or (high >= ema20 and close < ema20 and close < open)

// 6. Volume dry-up during rally (weak rally)
inverseSurfVolCondition = hasVolumeDryUp

// --- Combine Inverse Surf Conditions ---
inverseSurfDowntrendCondition = requireDowntrend ? isDowntrend : true
inverseSurfPriceCondition = requirePriceBelowMAs ? (close < ema20 and close < sma50) : true

inverseSurfSignal = enableInverseSurf and inverseSurfDowntrendCondition and inverseSurfPriceCondition and notCollapsedShort and isRallyToMA and isRallyEnding and inverseSurfVolCondition

// ============================================================================
// INVERSE TURF TRADE CONDITIONS (SHORT)
// Price breaking below clumped MAs after being above
// ============================================================================

// 1. MAs are clumped together (same as long turf - masAreClumped already calculated)

// 2. Price breaks below all three MAs (crosses below all in recent sessions)
priceBelowAllMAs = close < ema10 and close < ema20 and close < sma50

// Check if price was above at least one MA recently
priceWasAboveMA = false
for i = 1 to reclaimLookback
    if close[i] > ema10[i] or close[i] > ema20[i] or close[i] > sma50[i]
        priceWasAboveMA := true
        break

// 3. The breakdown happens with conviction (close weak)
breakdownWithConviction = close < open and (high - close) > (high - low) * 0.4

// 4. EMA crossunder potential (EMA10 crossing below EMA20, or about to)
emaCrossunder = ta.crossunder(ema10, ema20)
emaCrossunderRecent = emaCrossunder or emaCrossunder[1] or emaCrossunder[2]

// --- Combine Inverse Turf Conditions ---
inverseTurfSignal = enableInverseTurf and masAreClumped and priceBelowAllMAs and priceWasAboveMA and breakdownWithConviction and (emaCrossunderRecent or emasConverging)

// ============================================================================
// SIGNAL DISPLAY
// ============================================================================

// Calculate dot positions
dotPositionBelow = low - (atr * dotOffset)
dotPositionAbove = high + (atr * dotOffset)

// --- LONG SIGNALS (dots BELOW candles) ---
plotshape(turfSignal and not surfSignal ? dotPositionBelow : na, title="Turf Signal (Long)", location=location.absolute, style=shape.circle, size=size.small, color=turfColor)
plotshape(surfSignal and not turfSignal ? dotPositionBelow : na, title="Surf Signal (Long)", location=location.absolute, style=shape.circle, size=size.small, color=surfColor)
plotshape(surfSignal and turfSignal ? dotPositionBelow : na, title="Both Long Signals", location=location.absolute, style=shape.circle, size=size.small, color=color.yellow)

// --- SHORT SIGNALS (dots ABOVE candles) ---
plotshape(inverseTurfSignal and not inverseSurfSignal ? dotPositionAbove : na, title="Inverse Turf Signal (Short)", location=location.absolute, style=shape.circle, size=size.small, color=inverseTurfColor)
plotshape(inverseSurfSignal and not inverseTurfSignal ? dotPositionAbove : na, title="Inverse Surf Signal (Short)", location=location.absolute, style=shape.circle, size=size.small, color=inverseSurfColor)
plotshape(inverseSurfSignal and inverseTurfSignal ? dotPositionAbove : na, title="Both Short Signals", location=location.absolute, style=shape.circle, size=size.small, color=color.orange)

// ============================================================================
// SCREENER OUTPUTS (for Pine Screener compatibility)
// ============================================================================

// Boolean outputs for screener - LONG signals
plot(surfSignal ? 1 : 0, title="Surf Signal (Long)", display=display.data_window)
plot(turfSignal ? 1 : 0, title="Turf Signal (Long)", display=display.data_window)
plot(surfSignal and turfSignal ? 1 : 0, title="Both Long Signals", display=display.data_window)

// Boolean outputs for screener - SHORT signals
plot(inverseSurfSignal ? 1 : 0, title="Inverse Surf Signal (Short)", display=display.data_window)
plot(inverseTurfSignal ? 1 : 0, title="Inverse Turf Signal (Short)", display=display.data_window)
plot(inverseSurfSignal and inverseTurfSignal ? 1 : 0, title="Both Short Signals", display=display.data_window)

// Combined signal outputs
plot(surfSignal or turfSignal ? 1 : 0, title="Any Long Signal", display=display.data_window)
plot(inverseSurfSignal or inverseTurfSignal ? 1 : 0, title="Any Short Signal", display=display.data_window)
plot(surfSignal or turfSignal or inverseSurfSignal or inverseTurfSignal ? 1 : 0, title="Any Entry Signal", display=display.data_window)

// Numeric outputs for additional filtering in screener
plot(atrMultipleFrom50, title="ATR Multiple from 50 SMA", display=display.data_window)
plot(maSpread, title="MA Spread %", display=display.data_window)
plot(relativeVol, title="Relative Volume", display=display.data_window)

// Trend direction indicator (-1 = downtrend, 0 = neutral, 1 = uptrend)
trendDirection = isUptrend ? 1 : (isDowntrend ? -1 : 0)
plot(trendDirection, title="Trend Direction", display=display.data_window)

// ============================================================================
// ALERTS
// ============================================================================

// Long alerts
alertcondition(surfSignal, title="Surf Trade Entry (Long)", message="Surf Trade Signal: Pullback to MA with volume dry-up detected on {{ticker}}")
alertcondition(turfSignal, title="Turf Trade Entry (Long)", message="Turf Trade Signal: Price reclaiming clumped MAs on {{ticker}}")
alertcondition(surfSignal or turfSignal, title="Any Long Entry Signal", message="Long Entry Signal detected on {{ticker}}")

// Short alerts
alertcondition(inverseSurfSignal, title="Inverse Surf Trade Entry (Short)", message="Inverse Surf Signal: Rally to MA with volume dry-up detected on {{ticker}} - SHORT setup")
alertcondition(inverseTurfSignal, title="Inverse Turf Trade Entry (Short)", message="Inverse Turf Signal: Price breaking below clumped MAs on {{ticker}} - SHORT setup")
alertcondition(inverseSurfSignal or inverseTurfSignal, title="Any Short Entry Signal", message="Short Entry Signal detected on {{ticker}}")

// Combined alerts
alertcondition(surfSignal or turfSignal or inverseSurfSignal or inverseTurfSignal, title="Any Entry Signal (Long or Short)", message="Entry Signal detected on {{ticker}}")
```

---

## Related

- [[surf trade]]
- [[turf trade]]
- [[mark minervini (key moving averages)]]
- [[ATR%]]
- [[atr% multiple from 50ma]]
- [[relative volume at time]]
- [[extended stocks]]
- [[breakdown]]
- [[tradingview indicators]]

---

#tradingview #pine-script #setup #entry #surf-trade #turf-trade #screener #short-selling
