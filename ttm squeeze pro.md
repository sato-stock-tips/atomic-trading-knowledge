personal note: how i use this is to make sure that the histogram is trending in the correct direction of my trade; ie. when i am looking to long, i am looking to see if the histogram is trending upwards and vice versa

additionally when doing turf trades, i look to the ttm squeeze pro indicator to look for red dots

---

Credits: -> John Carter creating the TTM Squeeze and TTM Squeeze Pro -> Lazybear's original interpretation of the TTM Squeeze: Squeeze Momentum Indicator -> Makit0's evolution of Lazybear's script to factor in the TTM Squeeze Pro upgrades - Squeeze PRO Arrows

This is my version of their collective works, with amendments primarily to the Squeeze Conditions to more accurately reflect the color coding used by the official TMM Squeeze Pro indicator.

For those unfamiliar with the TTM Squeeze, it is simply a visual way of seeing how Bollinger Bands (standard deviations from a simple moving average) relate to Keltner Channels (average true range bands) compared with the momentum of the price action. The concept is that as Bollinger Bands compress within Keltner Channels, price volatility decreases, giving way for a potential explosive price movement up or down.

Differences between the original TTM Squeeze and TTM Squeeze Pro: -> Both use a 2 standard deviation Bollinger Band; -> The original squeeze only used a 1.5 ATR Keltner Channel; and -> The pro version uses 1.0, 1.5 and 2.0 ATR Keltner Channels. The pro version therefore helps differentiate between levels of squeeze (compression) as the Bollinger Bands moves through the Keltner Channels i.e. the greater the compression, the more potential for explosive moves - less compression means more squeezing.

The Histogram shows price momentum whereas the colored dots (along the zeroline) show where the Bollinger Bands are in relation to the Keltner Channels: -> Cyan Bars = positive, increasing momentum; -> Blue Bars = positive, decreasing momentum (indication of a reversal in price direction); -> Red Bars = negative, increasing momentum; -> Yellow Bars = negative, decreasing momentum (indication of a reversal in price direction); -> Orange Dots = High Compression / large squeeze (One or both of the Bollinger Bands is inside the 1st (1.0 ATR) Keltner Channel); -> Red Dots = Medium Squeeze (One or both of the Bollinger Bands is inside the 2nd (1.5 ATR) Keltner Channel); -> Black Dots = Low compression / wide squeeze (One or both of the Bollinger Bands is inside the 3rd (2.0 ATR) Keltner Channels); -> Green Dots = No Squeeze / Squeeze Fired (One or both of the Bollinger Bands is outside of the 3rd (2.0 ATR) Keltner Channel).

Ideal Scenario: As the ticker enters the squeeze, black dots would warn of the beginning of a low compression squeeze. As the Bollinger bands continue to constrict within the Keltner Channels, red dots would highlight a medium compression. As the price action and momentum continues to compress an orange dot shows warning of high compression. As price action leaves the squeeze, the coloring would reverse e.g. orange to red to black to green. Any compression squeeze is considered fired at the first green dot that appears.

Note: This is an ideal progression of the different types of squeezes, however any type of squeeze (and color sequence) may appear at anytime, therefore the focus is primarily on the green dots after any type of compression.

Entry and Exit Guide: -> John Carter recommends entering a position after at least 5 black dots or wait for 1st green dot; and -> Exit on second blue or yellow bar or, alternatively, remain in the position after confirming a continuing trend through a separate indicator.

---

read source code only if unclear

```// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © Beardy_Fred

//@version=5
indicator('Beardy Squeeze Pro', shorttitle='Squeeze', overlay=false, precision=2)

length = input.int(20, "TTM Squeeze Length")

//BOLLINGER BANDS
BB_mult = input.float(2.0, "Bollinger Band STD Multiplier")
BB_basis = ta.sma(close, length)
dev = BB_mult * ta.stdev(close, length)
BB_upper = BB_basis + dev
BB_lower = BB_basis - dev

//KELTNER CHANNELS
KC_mult_high = input.float(1.0, "Keltner Channel #1")
KC_mult_mid = input.float(1.5, "Keltner Channel #2")
KC_mult_low = input.float(2.0, "Keltner Channel #3")
KC_basis = ta.sma(close, length)
devKC = ta.sma(ta.tr, length)
KC_upper_high = KC_basis + devKC * KC_mult_high
KC_lower_high = KC_basis - devKC * KC_mult_high
KC_upper_mid = KC_basis + devKC * KC_mult_mid
KC_lower_mid = KC_basis - devKC * KC_mult_mid
KC_upper_low = KC_basis + devKC * KC_mult_low
KC_lower_low = KC_basis - devKC * KC_mult_low

//SQUEEZE CONDITIONS
NoSqz = BB_lower < KC_lower_low or BB_upper > KC_upper_low //NO SQUEEZE: GREEN
LowSqz = BB_lower >= KC_lower_low or BB_upper <= KC_upper_low //LOW COMPRESSION: BLACK
MidSqz = BB_lower >= KC_lower_mid or BB_upper <= KC_upper_mid //MID COMPRESSION: RED
HighSqz = BB_lower >= KC_lower_high or BB_upper <= KC_upper_high //HIGH COMPRESSION: ORANGE

//MOMENTUM OSCILLATOR
mom = ta.linreg(close - math.avg(math.avg(ta.highest(high, length), ta.lowest(low, length)), ta.sma(close, length)), length, 0)

//MOMENTUM HISTOGRAM COLOR
iff_1 = mom > nz(mom[1]) ? color.new(color.aqua, 0) : color.new(#2962ff, 0)
iff_2 = mom < nz(mom[1]) ? color.new(color.red, 0) : color.new(color.yellow, 0)
mom_color = mom > 0 ? iff_1 : iff_2

//SQUEEZE DOTS COLOR
sq_color = HighSqz ? color.new(color.orange, 0) : MidSqz ? color.new(color.red, 0) : LowSqz ? color.new(color.black, 0) : color.new(color.green, 0)

//ALERTS
Detect_Sqz_Start = input.bool(true, "Alert Price Action Squeeze")
Detect_Sqz_Fire = input.bool(true, "Alert Squeeze Firing")

if Detect_Sqz_Start and NoSqz[1] and not NoSqz
    alert("Squeeze Started")
else if Detect_Sqz_Fire and NoSqz and not NoSqz[1]
    alert("Squeeze Fired")

//PLOTS
plot(mom, title='MOM', color=mom_color, style=plot.style_columns, linewidth=2)
plot(0, title='SQZ', color=sq_color, style=plot.style_circles, linewidth=3)


```

link: https://www.tradingview.com/v/0drMdHsO/

#tradingview #indicator 