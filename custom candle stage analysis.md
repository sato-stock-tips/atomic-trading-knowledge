I use this script to create candles on the tradingview screenshots instead of using the standard candles template 

candles seen on the screenshot will follow the source code below

---


// About This Script: Custom Candle Stage Analysis Indicator

// ========================

//

// Overview

// This Pine Script v5 indicator, titled "Custom Candle Stage Analysis," is designed for use on TradingView charts.

// It provides a visual way to analyze market phases or "stages" by coloring price bars or candles according to predefined market conditions.

// Inspired by stage analysis concepts (such as those popularized by traders like Stan Weinstein and adapted from @TradeUdeNYC's work),

// it helps traders identify [[trending market]], [[reversal]], breakouts, and exhaustion points in a stock, forex, crypto, or any other instrument's price action.

//

// The core idea is to classify each candle into one of several stages based on the relationship between price, moving averages,

// volatility (via [[ATR%]]), and momentum indicators. This allows users to quickly spot whether the market is in a bullish buildup,

// a strong [[trending market]], a potential fade (pullback), a bearish downturn, or a neutral basing phase.

// By overlaying these colored stages directly on the price chart, the indicator simplifies technical analysis,

// making it easier to align trades with the broader market context.

//

// Unlike simple [[key moving averages]]crossovers, this script incorporates multiple layers of logic, including trend alignment,

// breakout detection, and distance from key averages measured in ATR multiples. It's particularly useful for swing traders,

// trend followers, or anyone employing stage-based strategies, as it highlights transitions between [[institutional accumulation]] (building up),

// markup (uptrends), distribution (fading), and markdown (downtrends).

//

// How It Works

// The indicator calculates stages using a combination of exponential moving averages (EMAs), a simple moving average (SMA),

// and the Average True Range (ATR) for volatility-adjusted measurements. Here's a step-by-step breakdown of the methodology:

//

// 1. Core Calculations:

// - Moving Averages: Computes EMAs of lengths 5, 10, 20, and 50 periods (customizable), plus a 50-period SMA.

// - ATR Multiples: Price deviation from the SMA50 is measured in ATR units to identify overextensions.

// - Trend Alignment: Checks if price and EMAs are stacked bullishly or bearishly, with optional slope checks.

// - Breakouts/Breakdowns: Detects closes above recent highs or below recent lows over a configurable lookback.

// - Spread and Proximity: Measures MA compression and price closeness to SMA50 for basing detection.

// - Transitional Conditions: Handles fading, exhaustion, and mean reversion scenarios.

//

// 2. Stage Determination:

// - Evaluates conditions in priority order to assign one of 12 stages to each bar.

// - Stages are grouped into bullish (Stage 2), bearish (Stage 4), neutral/basing (Stage 1), and fading (Stage 3).

//

// 3. Visualization:

// - Colors bars or candles based on stage and close direction (solid for up closes, translucent for down closes).

// - Optional labels display the stage name above each bar.

// - Switch between "Bars" or "Candles" via settings.

//

// Detailed Stage Descriptions

// - Stage 1A: Basing (Yellow) – Tight consolidation near SMA50 with compressed MAs; potential setup for breakout.

// - Stage 1B: Mean Reversion (Lime Green) – Choppy action reverting to the mean without clear trend.

// - Stage 2A: Bullish Trend (Bright Green) – Strong uptrend with bullish alignment.

// - Stage 2B: Breakout Confirm (Medium Green) – Bullish trend with confirmed breakout.

// - Stage 2C: Extended Bullish (>7x ATR) (Dark Green) – Overextended uptrend; caution for pullbacks.

// - Stage 2D: Exhausted Bullish (>11x ATR) (Teal) – Extreme extension; high reversal risk.

// - Stage 3A: Bullish Fade (Blue) – Pullback in bullish context; potential dip-buying opportunity.

// - Stage 3B: Fade Confirm (Light Blue) – Confirmed short-term weakness within broader bullishness.

// - Stage 4A: Bearish Trend (Red) – Strong downtrend with bearish alignment.

// - Stage 4B: Breakdown Confirm (Dark Red) – Bearish trend with confirmed breakdown.

// - Stage 4C: Extended Bearish (<-7x ATR) (Purple) – Overextended downtrend; potential for bounces.

// - Stage NA: Undefined (Gray) – Rare neutral cases that don't fit other criteria.

//

// Customization Options

// - ATR Length (default 14)

// - EMA/SMA Lengths (5, 10, 20, 50)

// - Breakout/Breakdown Lookback (default 20)

// - Basing Band ATR multiplier (default 1.0)

// - MA Compression ATR multiplier (default 1.5)

// - Plot Style: Bars or Candles

// - Show Stage Label (toggle)

//

// Practical Usage

// Use stages to filter trades: long in Stage 2, short in Stage 4, sidelined in Stage 1/NA.

// Watch stage transitions for entries/exits. Pair with volume or other indicators for confirmation.

// Works on any timeframe but shines on daily/weekly for swing trading.

//

// Limitations

// Reactive indicator; no guarantees. Backtest thoroughly and always use risk management.





```


  

//@version=6

indicator('Custom Candle Stage Analysis', overlay = true, max_labels_count = 500, max_lines_count = 500)

  

// === Stage Analysis Inputs ===

stage_group = 'Stage Analysis Settings'

lenATR = input.int(14, 'ATR Length (Wilder)', group = stage_group)

lenEMAS = input.int(5, 'EMAS Length', group = stage_group)

lenEMA10 = input.int(10, 'EMA10 Length', group = stage_group)

lenEMA20 = input.int(20, 'EMA20 Length', group = stage_group)

lenEMA50 = input.int(50, 'EMA50 Length', group = stage_group)

lenMS50 = input.int(50, 'SMA50 Length', group = stage_group)

boLookback = input.int(20, 'Breakout/Breakdown Lookback', minval = 5, group = stage_group)

basingBandATR = input.float(1.0, '1A: Price within ATR of SMA50', step = 0.1, group = stage_group)

macompressionATR = input.float(1.5, '1A: MA spread < X ATR', step = 0.1, group = stage_group)

plotStyle = input.string('Candles', 'Plot Style', options = ['Bars', 'Candles'], group = stage_group)

  

// === Stage Analysis Core Calculations ===

ema5 = ta.ema(close, lenEMAS)

ema10 = ta.ema(close, lenEMA10)

ema20 = ta.ema(close, lenEMA20)

ema50 = ta.ema(close, lenEMA50)

sma50 = ta.sma(close, lenMS50)

atr = ta.atr(lenATR)

  

// ATR multiple from SMA50 (signed)

atrx = (close - sma50) / atr

atrx1 = (close[1] - sma50[1]) / atr[1]

atrx2 = (close[2] - sma50[2]) / atr[2]

atrxABs = math.abs(atrx)

  

maSpread = math.max(math.max(ema10, ema20), sma50) - math.min(math.min(ema10, ema20), sma50)

  

// Trend alignment (+ slope)

ema10Up = ema10 > ema10[1]

ema20Up = ema20 > ema20[1]

ema50Up = sma50 > sma50[1]

upAlign = close > ema10 and ema10 > ema20 and ema20 > sma50 // Optionally add slope conditions: and ema10Up and ema20Up and not ema50Up

downAlign = close < ema10 and ema10 < ema20 and ema20 < sma50 // Optionally add: and not ema10Up and not ema20Up and not sma50Up

  

// Breakout / Breakdown

breakout = close > ta.highest(high, boLookback)[1]

breakdown = close < ta.lowest(low, boLookback)[1]

  

// Extensions

extBull = upAlign and atrx > 7 // Note: Original has a duplicate line with >11; adjust as needed

extBear = downAlign and atrx < -7

  

// Transitional proxies

basing = math.abs(close - sma50) < basingBandATR * atr and maSpread < macompressionATR * atr

meanRev = not upAlign and not downAlign and not basing and (close > ema20 and ema10 > ema20 or ema10 > ema20 and close < ema10) // Simplified; adjust per original logic

fadeA = not extBull and close > sma50 and (upAlign and (close < ema10 or not ema10Up) or ema10 >= ema20 and close < ema10)

exhA = extBull and close > sma50 and (upAlign and (close < ema10 or not ema10Up) or ema10 >= ema20 and close < ema10)

fadeB = not extBear and close < sma50 and (downAlign and (close > ema10 or not ema10Up) or ema10 <= ema20 and close > ema10) // Mirror for bearish; adjust if needed

  

// === Stage selection ===

ST_1A = 1

ST_1B = 2

ST_2A = 3

ST_2B = 4

ST_2C = 5

ST_2D = 6 // Added for exhaustion

ST_3A = 7

ST_3B = 8

ST_4A = 9

ST_4B = 10

ST_4C = 11

ST_NA = 12

  

stage = if extBull

ST_2C

else if extBear

ST_4C

else if upAlign

breakout ? ST_2B : ST_2A

else if exhA

ST_2D

else if downAlign

breakdown ? ST_4B : ST_4A

else if sma50 > ema10 and ema10 > ema20 // Additional conditions from original

ST_4A

else if fadeB

ST_3B

else if fadeA

ST_3A

else if meanRev

ST_1B

else if close < sma50 and close > ema10 and ema10 < ema20

ST_1A

else

ST_NA

  

stageName = if stage == ST_1A

'1A Basing'

else if stage == ST_1B

'1B Mean Reversion'

else if stage == ST_2A

'2A Bullish Trend'

else if stage == ST_2B

'2B Breakout Confirm'

else if stage == ST_2C

'2C Extended Bullish (>7x)'

else if stage == ST_2D

'2D Exhausted Bullish (>11x)'

else if stage == ST_3A

'3A Bullish Fade'

else if stage == ST_3B

'3B Fade Confirm'

else if stage == ST_4A

'4A Bearish Trend'

else if stage == ST_4B

'4B Breakdown Confirm'

else if stage == ST_4C

'4C Extended Bearish (< -7x)'

else

'NA Undefined'

  

// Colors (adjust RGB values to match your preference)

color c1A = color.rgb(255, 217, 0)

color c1Ad = color.rgb(255, 217, 0, 50)

color c1B = color.rgb(165, 206, 3)

color c1Bd = color.rgb(165, 206, 3, 50)

color c2A = color.rgb(89, 191, 94)

color c2Ad = color.rgb(89, 191, 94, 50)

color c2B = color.rgb(71, 153, 75)

color c2Bd = color.rgb(71, 153, 75, 50)

color c2C = color.rgb(46, 102, 55)

color c2Cd = color.rgb(46, 102, 55, 50)

color c2D = color.rgb(10, 105, 123)

color c2Dd = color.rgb(10, 105, 123, 50)

color c3A = color.rgb(63, 94, 205)

color c3Ad = color.rgb(63, 94, 205, 50)

color c3B = color.rgb(169, 206, 244)

color c3Bd = color.rgb(169, 206, 244, 50)

color c4A = color.rgb(255, 93, 93)

color c4Ad = color.rgb(255, 93, 93, 50)

color c4B = color.rgb(183, 0, 76)

color c4Bd = color.rgb(183, 0, 76, 50)

color c4C = color.rgb(182, 129, 234)

color c4Cd = color.rgb(182, 129, 234, 50)

color cNA = color.rgb(160, 160, 160)

color cNAd = color.rgb(160, 160, 160, 50)

  

// Neutral bars (rare ties) = gray

stageColor = if stage == ST_1A and close > close[1]

c1A

else if stage == ST_1A and close < close[1]

c1Ad

else if stage == ST_1B and close > close[1]

c1B

else if stage == ST_1B and close < close[1]

c1Bd

else if stage == ST_2A and close > close[1]

c2A

else if stage == ST_2A and close < close[1]

c2Ad

else if stage == ST_2B and close > close[1]

c2B

else if stage == ST_2B and close < close[1]

c2Bd

else if stage == ST_2C and close > close[1]

c2C

else if stage == ST_2C and close < close[1]

c2Cd

else if stage == ST_2D and close > close[1]

c2D

else if stage == ST_2D and close < close[1]

c2Dd

else if stage == ST_3A and close > close[1]

c3A

else if stage == ST_3A and close < close[1]

c3Ad

else if stage == ST_3B and close > close[1]

c3B

else if stage == ST_3B and close < close[1]

c3Bd

else if stage == ST_4A and close > close[1]

c4A

else if stage == ST_4A and close < close[1]

c4Ad

else if stage == ST_4B and close > close[1]

c4B

else if stage == ST_4B and close < close[1]

c4Bd

else if stage == ST_4C and close > close[1]

c4C

else if stage == ST_4C and close < close[1]

c4Cd

else if stage == ST_NA and close > close[1]

cNA

else if stage == ST_NA and close < close[1]

cNAd

else

color.gray // Fallback

  

// Optional: text label (toggle in settings)

show_label = input.bool(false, 'Show Stage Label', inline = '1')

if show_label

label.new(bar_index, high, stageName, style = label.style_label_left, color = stageColor, textcolor = color.black, size = size.small)

  

// Display modes based on selection

show_bars = plotStyle == 'Bars' ? display.all : display.none

show_candles = plotStyle == 'Candles' ? display.all : display.none

  

barcolor(stageColor, title = 'Stage Bars', display = show_bars)

plotcandle(open, high, low, close, color = stageColor, wickcolor = stageColor, bordercolor = stageColor, title = 'Stage Candles', display = show_candles)
```

#tradingview #indicator 