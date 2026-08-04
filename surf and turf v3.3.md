# Surf & Turf Entry Signals v3.3 - Screener Optimized

## Changes from v3 → v3.3

### Screener Output Refinement
Reduced to exactly **10 filters** for Pine Screener compatibility:

| # | Output | Type | Description |
|---|--------|------|-------------|
| 1 | Both Long | Boolean | Surf AND Turf on same bar (high conviction) |
| 2 | Both Short | Boolean | Inverse Surf AND Inverse Turf on same bar |
| 3 | Any Long | Boolean | Surf OR Turf on current bar |
| 4 | Any Short | Boolean | Inverse Surf OR Inverse Turf on current bar |
| 5 | Any Entry | Boolean | Any signal on current bar |
| 6 | Any Long 3 Days | Boolean | Long signal within past 3 bars |
| 7 | Any Short 3 Days | Boolean | Short signal within past 3 bars |
| 8 | Any Entry 3 Days | Boolean | Any signal within past 3 bars |
| 9 | ATR Multiple from 50 SMA | Numeric | Extension measurement |
| 10 | MA Spread % | Numeric | MA clumping measurement |

### Removed from v3
- Single-bar Surf/Turf/Inverse signals (redundant with "Any" versions)
- Relative Volume (to make room for more signal variants)
- Trend Direction (not essential for entry filtering)

---

## Pine Script Code

```pinescript
// This Pine Script is intended for educational purposes only created by @Jimmytrustfund
// Surf & Turf Entry Signals v3.3 - Screener Optimized
// Based on Qullamaggie's pullback (surf) and clumped MA reclaim (turf) setups
// Includes inverse setups for short-side trades

//@version=6
indicator('Surf & Turf Entry Signals v3.3', overlay = true)

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
turfVolLookback = input.int(10, 'Turf Consolidation Lookback (days)', minval = 5, maxval = 20, group = grpTurf, tooltip = 'Days to check for consolidation volume dry-up')
turfReclaimVolThreshold = input.float(1.0, 'Min Reclaim Volume (x avg)', minval = 0.5, maxval = 3.0, step = 0.1, group = grpTurf, tooltip = 'Minimum relative volume on reclaim candle')

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

// Count dry-up days (for surf) - Screener compatible: no break, no dangling variables
dryUpCount = int(0)
dryUpCount := volume < volSMA * volDryUpThreshold ? dryUpCount + 1 : dryUpCount
dryUpCount := volume[1] < volSMA[1] * volDryUpThreshold ? dryUpCount + 1 : dryUpCount
dryUpCount := volDryUpDays > 2 and volume[2] < volSMA[2] * volDryUpThreshold ? dryUpCount + 1 : dryUpCount
dryUpCount := volDryUpDays > 3 and volume[3] < volSMA[3] * volDryUpThreshold ? dryUpCount + 1 : dryUpCount
dryUpCount := volDryUpDays > 4 and volume[4] < volSMA[4] * volDryUpThreshold ? dryUpCount + 1 : dryUpCount

hasVolumeDryUp = dryUpCount >= volDryUpDays

// ============================================================================
// TREND CONDITIONS (Shared)
// ============================================================================

// Uptrend condition: EMA10 > EMA20 > SMA50
isUptrend = ema10 > ema20 and ema20 > sma50

// Downtrend condition: EMA10 < EMA20 < SMA50
isDowntrend = ema10 < ema20 and ema20 < sma50

// ============================================================================
// SURF TRADE CONDITIONS (LONG)
// The last daily candle during pullback before it pumps higher again
// ============================================================================

// 1. Price above key MAs (for trend confirmation)
priceAboveMAs = close > ema10 and close > ema20 and close > sma50

// 2. Not extended: less than 4x ATR% from 50 SMA (positive direction)
notExtendedLong = atrMultipleFrom50 < maxATRMultipleFrom50 and atrMultipleFrom50 > 0

// 3. Price pulled back to within 1-1.5 ATR of supporting EMA (EMA10 or EMA20)
distToEMA10 = math.abs(low - ema10)
distToEMA20 = math.abs(low - ema20)
atrThresholdValue = atr * pullbackATRThreshold

// Price is pulling back to EMA (touching or within threshold)
touchingEMA10Long = distToEMA10 <= atrThresholdValue and low <= ema10 * 1.02
touchingEMA20Long = distToEMA20 <= atrThresholdValue and low <= ema20 * 1.02
isPullbackToMA = touchingEMA10Long or touchingEMA20Long

// 4. Pullback characteristics: recent down/flat days followed by potential reversal
recentPullback = close[1] < close[2] or close[2] < close[3]
closeNearHigh = close - low > (high - low) * 0.5
bounceFromMA = close > open and low <= math.max(ema10, ema20) * 1.01

isPullbackEnding = recentPullback and (closeNearHigh or bounceFromMA) or low <= ema20 and close > ema20 and close > open

// 5. Volume dry-up during pullback
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

// 2. Uptrend required (v3: added trend alignment)
turfTrendAligned = isUptrend

// 3. Volume validation (v3: added consolidation dry-up + reclaim volume)
// Count dry-up days during consolidation (skip today and yesterday) - Screener compatible
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
consolidationDryUpCount := volume[11] < volSMA[11] * volDryUpThreshold ? consolidationDryUpCount + 1 : consolidationDryUpCount

hasConsolidationDryUp = consolidationDryUpCount >= volDryUpDays

// Reclaim volume on signal candle
reclaimVolume = relativeVol > turfReclaimVolThreshold

// Combined volume condition
turfVolumeCondition = hasConsolidationDryUp and reclaimVolume

// 4. Price reclaims all three MAs (crosses above all in recent sessions)
priceAboveAllMAs = close > ema10 and close > ema20 and close > sma50

// Check if price was below at least one MA recently - Screener compatible (no break)
priceWasBelowMA = close[1] < ema10[1] or close[1] < ema20[1] or close[1] < sma50[1] or 
                  (reclaimLookback > 1 and (close[2] < ema10[2] or close[2] < ema20[2] or close[2] < sma50[2])) or
                  (reclaimLookback > 2 and (close[3] < ema10[3] or close[3] < ema20[3] or close[3] < sma50[3])) or
                  (reclaimLookback > 3 and (close[4] < ema10[4] or close[4] < ema20[4] or close[4] < sma50[4])) or
                  (reclaimLookback > 4 and (close[5] < ema10[5] or close[5] < ema20[5] or close[5] < sma50[5]))

// 5. The reclaim happens with some conviction (close strong)
reclaimWithConviction = close > open and close - low > (high - low) * 0.4

// 6. EMA crossover potential (EMA10 crossing above EMA20, or about to)
emaCrossover = ta.crossover(ema10, ema20)
emaCrossoverRecent = emaCrossover or emaCrossover[1] or emaCrossover[2]
emasConverging = math.abs(ema10 - ema20) / ema20 * 100 < 1.0

// --- Combine Turf Conditions (v3: added trend and volume) ---
turfSignal = enableTurf and turfTrendAligned and masAreClumped and turfVolumeCondition and priceAboveAllMAs and priceWasBelowMA and reclaimWithConviction and (emaCrossoverRecent or emasConverging)

// ============================================================================
// INVERSE SURF TRADE CONDITIONS (SHORT)
// The last daily candle during rally before it dumps lower again
// ============================================================================

// 1. Price below key MAs (for trend confirmation)
priceBelowMAs = close < ema10 and close < ema20 and close < sma50

// 2. Not collapsed: price should be within 4x ATR% BELOW the 50 SMA
notCollapsedShort = atrMultipleFrom50 > -maxATRMultipleFrom50 and atrMultipleFrom50 < 0

// 3. Price rallied back to within 1-1.5 ATR of resistance EMA (EMA10 or EMA20)
distHighToEMA10 = math.abs(high - ema10)
distHighToEMA20 = math.abs(high - ema20)

// Price is rallying to EMA (touching or within threshold from above)
touchingEMA10Short = distHighToEMA10 <= atrThresholdValue and high >= ema10 * 0.98
touchingEMA20Short = distHighToEMA20 <= atrThresholdValue and high >= ema20 * 0.98
isRallyToMA = touchingEMA10Short or touchingEMA20Short

// 4. Rally characteristics: recent up/flat days followed by potential reversal
recentRally = close[1] > close[2] or close[2] > close[3]
closeNearLow = high - close > (high - low) * 0.5
rejectionFromMA = close < open and high >= math.min(ema10, ema20) * 0.99

isRallyEnding = recentRally and (closeNearLow or rejectionFromMA) or high >= ema20 and close < ema20 and close < open

// 5. Volume dry-up during rally (weak rally)
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

// 2. Downtrend required (v3: added trend alignment)
inverseTurfTrendAligned = isDowntrend

// 3. Volume validation (v3: added consolidation dry-up + breakdown volume)
// Uses same consolidation dry-up logic as long turf
inverseTurfVolumeCondition = hasConsolidationDryUp and reclaimVolume

// 4. Price breaks below all three MAs (crosses below all in recent sessions)
priceBelowAllMAs = close < ema10 and close < ema20 and close < sma50

// Check if price was above at least one MA recently - Screener compatible (no break)
priceWasAboveMA = close[1] > ema10[1] or close[1] > ema20[1] or close[1] > sma50[1] or 
                  (reclaimLookback > 1 and (close[2] > ema10[2] or close[2] > ema20[2] or close[2] > sma50[2])) or
                  (reclaimLookback > 2 and (close[3] > ema10[3] or close[3] > ema20[3] or close[3] > sma50[3])) or
                  (reclaimLookback > 3 and (close[4] > ema10[4] or close[4] > ema20[4] or close[4] > sma50[4])) or
                  (reclaimLookback > 4 and (close[5] > ema10[5] or close[5] > ema20[5] or close[5] > sma50[5]))

// 5. The breakdown happens with conviction (close weak)
breakdownWithConviction = close < open and high - close > (high - low) * 0.4

// 6. EMA crossunder potential (EMA10 crossing below EMA20, or about to)
emaCrossunder = ta.crossunder(ema10, ema20)
emaCrossunderRecent = emaCrossunder or emaCrossunder[1] or emaCrossunder[2]

// --- Combine Inverse Turf Conditions (v3: added trend and volume) ---
inverseTurfSignal = enableInverseTurf and inverseTurfTrendAligned and masAreClumped and inverseTurfVolumeCondition and priceBelowAllMAs and priceWasAboveMA and breakdownWithConviction and (emaCrossunderRecent or emasConverging)

// ============================================================================
// SIGNAL DISPLAY (Chart Overlay)
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
// SCREENER OUTPUTS - EXACTLY 10 FILTERS (v3.3)
// ============================================================================

// Signal calculations for 1-day and 3-day variants
anyLong = surfSignal or turfSignal
anyShort = inverseSurfSignal or inverseTurfSignal
anyEntry = anyLong or anyShort

anyLong3Days = anyLong or anyLong[1] or anyLong[2]
anyShort3Days = anyShort or anyShort[1] or anyShort[2]
anyEntry3Days = anyEntry or anyEntry[1] or anyEntry[2]

bothLong = surfSignal and turfSignal
bothShort = inverseSurfSignal and inverseTurfSignal

// 1. Both Long (Surf AND Turf same bar)
plot(bothLong ? 1 : 0, title = 'Both Long', display = display.data_window)

// 2. Both Short (Inverse Surf AND Inverse Turf same bar)
plot(bothShort ? 1 : 0, title = 'Both Short', display = display.data_window)

// 3. Any Long (1-day) - Surf OR Turf
plot(anyLong ? 1 : 0, title = 'Any Long', display = display.data_window)

// 4. Any Short (1-day) - Inverse Surf OR Inverse Turf
plot(anyShort ? 1 : 0, title = 'Any Short', display = display.data_window)

// 5. Any Entry (1-day) - Any Long OR Any Short
plot(anyEntry ? 1 : 0, title = 'Any Entry', display = display.data_window)

// 6. Any Long 3 Days
plot(anyLong3Days ? 1 : 0, title = 'Any Long 3 Days', display = display.data_window)

// 7. Any Short 3 Days
plot(anyShort3Days ? 1 : 0, title = 'Any Short 3 Days', display = display.data_window)

// 8. Any Entry 3 Days
plot(anyEntry3Days ? 1 : 0, title = 'Any Entry 3 Days', display = display.data_window)

// 9. ATR Multiple from 50 SMA (Numeric)
plot(atrMultipleFrom50, title = 'ATR Multiple from 50 SMA', display = display.data_window)

// 10. MA Spread % (Numeric)
plot(maSpread, title = 'MA Spread %', display = display.data_window)

// ============================================================================
// ALERTS
// ============================================================================

// Long alerts
alertcondition(surfSignal, title = 'Surf Trade Entry (Long)', message = 'Surf Trade Signal: Pullback to MA with volume dry-up detected on {{ticker}}')
alertcondition(turfSignal, title = 'Turf Trade Entry (Long)', message = 'Turf Trade Signal: Price reclaiming clumped MAs with volume confirmation on {{ticker}}')
alertcondition(surfSignal or turfSignal, title = 'Any Long Entry Signal', message = 'Long Entry Signal detected on {{ticker}}')

// Short alerts
alertcondition(inverseSurfSignal, title = 'Inverse Surf Trade Entry (Short)', message = 'Inverse Surf Signal: Rally to MA with volume dry-up detected on {{ticker}} - SHORT setup')
alertcondition(inverseTurfSignal, title = 'Inverse Turf Trade Entry (Short)', message = 'Inverse Turf Signal: Price breaking below clumped MAs with volume confirmation on {{ticker}} - SHORT setup')
alertcondition(inverseSurfSignal or inverseTurfSignal, title = 'Any Short Entry Signal', message = 'Short Entry Signal detected on {{ticker}}')

// Combined alerts
alertcondition(surfSignal or turfSignal or inverseSurfSignal or inverseTurfSignal, title = 'Any Entry Signal (Long or Short)', message = 'Entry Signal detected on {{ticker}}')
```

---

## Screener Output Summary (v3.3)

| # | Filter Name | Type | Use Case |
|---|-------------|------|----------|
| 1 | **Both Long** | Boolean | High conviction: surf + turf same bar |
| 2 | **Both Short** | Boolean | High conviction: inverse surf + inverse turf same bar |
| 3 | **Any Long** | Boolean | Current bar surf OR turf |
| 4 | **Any Short** | Boolean | Current bar inverse surf OR turf |
| 5 | **Any Entry** | Boolean | Current bar any signal |
| 6 | **Any Long 3 Days** | Boolean | Surf OR turf within 3 bars |
| 7 | **Any Short 3 Days** | Boolean | Inverse surf OR turf within 3 bars |
| 8 | **Any Entry 3 Days** | Boolean | Any signal within 3 bars |
| 9 | **ATR Multiple from 50 SMA** | Numeric | Filter by extension (e.g., < 4.0) |
| 10 | **MA Spread %** | Numeric | Filter by MA clumping (e.g., < 3.0) |

### Filter Count: Exactly 10 ✓

---

## Key Screener Filters You Can Use

### To find surf setups:
```
Any Long > 0
ATR Multiple from 50 SMA < 4.0
MA Spread % > 3.0 (ensures NOT a turf setup)
```

### To find turf setups:
```
Any Long > 0
MA Spread % < 3.0 (clumped MAs)
ATR Multiple from 50 SMA < 4.0
```

### To find high conviction setups:
```
Both Long > 0 (surf + turf same bar)
```

### To catch recent breakouts:
```
Any Long 3 Days > 0
ATR Multiple from 50 SMA < 4.0
```

---

## Related

- [[surf trade]]
- [[turf trade]]
- [[surf_turf_entry_signals]]
- [[surf_turf_entry_signals_v2.pine]]

---

#tradingview #pine-script #setup #entry #surf-trade #turf-trade #screener #short-selling
