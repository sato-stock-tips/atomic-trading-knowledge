this indicator is the one that supplies the simple and exponential moving averages on the chart screenshots

the name of this indicator is named after mark minervini

Just sharing a script that I made when I began to be interested in Mark Minervini, Wiliam O'Neil, Nicolas Darvas,.. trading style. 1

This script displays :

- 10 EMA (orange) 
    
- 20 EMA (blue light) 
    
- 50 SMA (blue)
    
- 150 SMA (green)
    
- 200 SMA (red)
    
- Shows when the stock is "extended" from EMA10 to high of the candle (Works in Daily, you can adjust the % to make it match with the stock's volatility) 7
    
    (Shows a red area between price and 10 EMA) 8
    
- Shows when Mark Minervini's trend template is respected by highlighting green between 150 & 200 SMA. 9
    

I Although added bollinger bands and 5 EMA for very strong stocks. (I never use them) 10

---
read source code only if necessary or unsure

```
// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © Fred6724

//@version=5
indicator('Mark Minervini', overlay=true)


// Input
bema5   = input(false, title='EMA 5', group='INDICATOR BASED ON PRICES', inline = '0')
cema5   = input(color.purple, title='Color', group='INDICATOR BASED ON PRICES', inline = '0')
bsma10  = input(false, title='SMA 10', group='INDICATOR BASED ON PRICES', inline = '0')
csma10  = input(color.red, title='Color', group='INDICATOR BASED ON PRICES', inline = '0')
bema10  = input(true, title='EMA 10', group='INDICATOR BASED ON PRICES', inline = '1')
cema10  = input(color.orange, title='Color', group='INDICATOR BASED ON PRICES', inline = '1')
bema20  = input(true, title='EMA 20', group='INDICATOR BASED ON PRICES', inline = '1')
cema20  = input(color.aqua, title='Color', group='INDICATOR BASED ON PRICES', inline = '1')
bsma50  = input(true, title='SMA 50', group='INDICATOR BASED ON PRICES', inline = '2')
csma50  = input(color.rgb(44, 138, 216), title='Color', group='INDICATOR BASED ON PRICES', inline = '2')
bsma150 = input(true,  title='SMA 150', group='INDICATOR BASED ON PRICES', inline = '2')
csma150  = input(color.lime, title='Color', group='INDICATOR BASED ON PRICES', inline = '2')
bsma200 = input(true, title='SMA 200', group='INDICATOR BASED ON PRICES', inline = '3')
csma200 = input(color.red, title='Color', group='INDICATOR BASED ON PRICES', inline = '3')
aire    = input(true, title='Marks Trend Template on 150-200SMA', group='INDICATOR BASED ON PRICES')
colorMM = input(color.rgb(0,230,118,80), title='Trend Template Color', group='INDICATOR BASED ON PRICES')
b52     = input(false, title='Display 52We Highs & Lows', group='INDICATOR BASED ON PRICES')

extend           = input(true, title='Daily Extended Detector', group='ASSISTANCE WITH PRICE READING')
colorExtended    = input(color.rgb(255,82,82,50), title='Extended Color', group='ASSISTANCE WITH PRICE READING')
iDay             = input(false, title='Inside Days Detector', group='ASSISTANCE WITH PRICE READING')
colorIDay        = input(color.white, title='Inside Day Color', group='ASSISTANCE WITH PRICE READING')
WtClose          = input(false, title='Weekly Tight Closes Detector', group='ASSISTANCE WITH PRICE READING')
colorTightCloses = input(color.aqua, title='Color of Tight Closes Boxes', group='ASSISTANCE WITH PRICE READING')


// SMA/EMA Calculation
ema5    = ta.ema(close,5)
ema10   = ta.ema(close,10)
sma10   = ta.sma(close,10)
ema102  = ta.ema(close,10) // invisible ema10 used to display the extended area otherwise it disappears when we deactivate the first ema10
ema20   = ta.ema(close,20)
sma50   = ta.sma(close,50)
sma150  = ta.sma(close,150)
sma1502 = ta.sma(close,150)  // invisible sma150 used to display the Mark Minervini Trend Template
sma200  = ta.sma(close,200)


// Ploting SMA/EMA
pema5   = plot(bema5  ?  ema5:na,   color=cema5)
pema10  = plot(bema10 ?  ema10:na,  color=cema10)
psma    = plot(bsma10 ?  sma10:na,  color=csma10)
pema20  = plot(bema20 ?  ema20:na,  color=cema20)
psma50  = plot(bsma50 ?  sma50:na,  color=csma50)
psma150 = plot(bsma150 ? sma150:na, color=csma150)
psma200 = plot(bsma200 ? sma200:na, color=csma200)


// Mark's Trend Template Criteria = Qualifier only.

// From 'Trade Like a Stock Market Wizard'
// Trend Template
// 1. The current stock price is above both the 150-day (30-week) and the 200-day 
// (40-week) moving average price lines.
// 2. The 150-day moving average is above the 200-day moving average.
// 3. The 200-day moving average line is trending up for at least 1 month (preferably 
// 4–5 months minimum in most cases).
// 4. The 50-day (10-week) moving average is above both the 150-day and 200-day 
// moving averages.
// 5. The current stock price is trading above the 50-day moving average.
// 6. The current stock price is at least 30 percent above its 52-week low. (Many of 
// the best selections will be 100 percent, 300 percent, or greater above their 
// 52-week low before they emerge from a solid consolidation period and mount 
// a large scale advance.)
// 7. The current stock price is within at least 25 percent of its 52-week high (the 
// closer to a new high the better).
// 8. The relative strength ranking (as reported in Investor’s Business Daily) is no 
// less than 70, and preferably in the 80s or 90s, which will generally be the case 
// with the better selections

//Cond 50>150>200
condTrade  = (sma50>sma150 and sma150>sma200) and close>sma50 // SMA 50 > SMA 150 > SMA 200 & Close > MM50
condTrade2 = (sma200>sma200[10] and sma200[10]>sma200[20] and sma200[20]>sma200[30]) // Rising 200 SMA
srcWe    = request.security(syminfo.tickerid, 'W', close)
highWe   = request.security(syminfo.tickerid, 'W', high)
lowWe    = request.security(syminfo.tickerid, 'W', low)
sma50Da  = request.security(syminfo.tickerid, 'D', sma50)
sma150Da = request.security(syminfo.tickerid, 'D', sma150)
sma200Da = request.security(syminfo.tickerid, 'D', sma200)


// Calculation of 52-week high and 52-week low
highestWe52 = request.security(syminfo.tickerid, 'W', ta.highest(high,52)) // 52-week high
lowestWe52  = request.security(syminfo.tickerid, 'W', ta.lowest(low,52)) // 52-week low
condTrade4 = (highestWe52)*75 <= close*100 //  The current stock price is within at least 25 percent of its 52-week high (the closer to a new high the better).
limitWe52 = (highestWe52)*75/100 // Calculation of the 25% threshold
condTrade3 = lowestWe52*130 <= close*100 // The current stock price is at least 30 percent above its 52-week low. 
ph52=plot(b52 ? highestWe52:na)
pl52=plot(b52 ? lowestWe52:na)
plimit52=plot(b52 ? limitWe52:na, color=color.lime, style=plot.style_stepline)
condTot = condTrade and condTrade2 and condTrade3 and condTrade4
lime=color.new(color.lime,100)
psma1502 = plot((aire and condTot) and timeframe.isdaily ? sma1502:na, color=lime)
fill(psma1502,psma200, color=colorMM)


//Extended to EMA10
// Daily Close/High
DClose  = request.security(syminfo.tickerid, 'D', close)
DHigh   = request.security(syminfo.tickerid, 'D', high)
// Daily EMA10
DEma10  = request.security(syminfo.tickerid, 'D', ta.ema(DClose, 10))
DEma102 = request.security(syminfo.tickerid, 'D', ta.ema(DClose, 10))


// Previous condition with fix %
//condExtended = extend and ((DHigh*100/DEma10)-100>extendp)
// Test f(ATR) to make it auto-adaptable
atrDa = request.security(syminfo.tickerid, 'D', ta.atr(14))
// If the high of the candle minus the price of ema10 is above 2,1 time the Da atr (In these cases, I prefere to use multiple of 3 -> See Nicolas Tesla)
condExtended = extend and (DHigh-DEma10)>(2.1*atrDa)
orange = color.new(color.orange,100)
pema102 = plot(condExtended and timeframe.isdaily ? DEma102:na, color=orange) // Plotting another Da EMA10 only when condition is meet seems to be the only way to colorise the way I want...
sma3 = ta.sma(close,1)
blue = color.new(color.blue, 100)
psma3 = plot(sma3, color=blue)
fill(psma3,pema102, color=colorExtended)


// Weekly Tight Closes Detector
tfWeekly = timeframe.isweekly
if(tfWeekly)
    // Open
    WkO2   = open[2]
    //Closes
    WkC    = close
    WkC1   = close[1]
    WkC2   = close[2]
    // Highs
    WkH    = high
    WkH1   = high[1]
    WkH2   = high[2]
    // Lows
    WkL    = low
    WkL1   = low[1]
    WkL2   = low[2]
    // WEMA
    Wema10 = ta.ema(close,10)
    Wema20 = ta.ema(close,20)
    // ATR Weekly (Used to have an auto-adaptive tight closes detector. Formula = Averages High-Low of the 14 previous bars. (Volatility measurement)
    atr    = ta.atr(14)
    // Conditions (I like to have 3 tiny candle with tight closes so I add High and Low cond as well)
    condTightClose = WkC < WkC1+(WkC1*atr/(close*2)) and WkC > WkC1-(WkC1*atr/(close*2)) and WkC1 < WkC2+(WkC2*atr/(close*2)) and WkC1 > WkC2-(WkC2*atr/(close*2)) and WkC < WkC2+(WkC2*atr/(close*2)) and WkC > WkC2-(WkC2*atr/(close*2))
    condTightHigh = WkH < WkH1+(WkH1*atr/(close*2)) and WkH > WkH1-(WkH1*atr/(close*2)) and WkH1 < WkH2+(WkH2*atr/(close*2)) and WkH1 > WkH2-(WkH2*atr/(close*2))
    condTightLow = WkL < WkL1+(WkL1*atr/(close*2)) and WkL > WkL1-(WkL1*atr/(close*2)) and WkL1 < WkL2+(WkL2*atr/(close*2)) and WkL1 > WkL2-(WkL2*atr/(close*2))
    //condNotLowerLows  = WkL2 > WkL1 and WkL1 > WkL
    // I would like the script not to show me 3 tight candles when the first candle of the three is nearly full and big
    // For that I wrote that the total size of the weekly wick of the candle must be 2 times bigger than the body
    // But I noticed somtimes very small candle with little or no wick are still valide so added an exception ! (Yes it is far-fetched)
    condFirstCandle = false
    // For positive bars
    if(WkC2 >= WkO2)
        condFirstCandle := WkH2 - WkC2 + WkO2 - WkL2 > 2*(WkC2 - WkO2) or (WkH2-WkL2<WkH1-WkL1)
    // For negative bars
    if(WkC2 < WkO2)
        condFirstCandle := WkH2 - WkO2 + WkC2 - WkL2 > 2*(WkO2 - WkC2) or (WkH2-WkL2<WkH1-WkL1)
    // All condition together
    condTot3WTight = condTightClose and (condTightHigh or condTightLow) and condFirstCandle //and not condNotLowerLows


    //Plot Boxes Arround Weekly Tight Closes
    highestW = ta.highest(WkH,3)
    lowestW  = ta.lowest (WkL,3)
    if(condTot3WTight and WtClose)
        box.new(bar_index[2], highestW, bar_index, lowestW, border_color = color.new(colorTightCloses,20), border_width = 1, border_style=line.style_dotted, bgcolor = color.new(colorTightCloses,85))

    

// Inside Bars Detector (All Timeframes)
condInside = iDay ? high[1] > high and low[1] < low:na
condBold   = condInside and condInside[1] // Means two inside days in a row
if(iDay and condInside)
    // I use boxes to colorize both up and down lines (Boxes with 100% transparency frames (Yes I cheat...! Always.))
    b = box.new(bar_index[1], high[1], bar_index, low[1], border_color = color.new(colorIDay,100), border_width = 2, border_style=line.style_dotted, bgcolor = color.new(colorIDay,90))
    l1 = line.new(bar_index[1], high[1], bar_index, high[1], color=colorIDay,  style =  line.style_dotted, extend = extend.none, width = condBold ? 2:1)
    l2 = line.new(bar_index[1], low[1], bar_index, low[1], color=colorIDay,  style =  line.style_dotted, extend = extend.none, width = condBold ? 2:1)

//------------------  Markerd Highs and Lows  ---------------------//

// Price Peak/Valley Points
// Highlights exact price at high or low points over a 19-period interval.
// For example, on a Daily chart, a High Price point is marked on the date
// where there has been no higher price the 9 days prior to that date and
// the 9 days following that date.

// Inputs
i_displayHL = input(true, title="Display H/L Points", group="High/Low Price Points")
i_colorHL   = input(color.rgb(255,255,255,0), title='Labels Color', group="High/Low Price Points")
i_displayPc = input(false, title="%Change", group='High/Low Price Points')
i_colorPctP = input(color.rgb(0, 0, 255), title='Positive % Color', group="High/Low Price Points", inline = "z")
i_colorPctN = input(color.rgb(222,50,174,0), title='Negative %', group="High/Low Price Points", inline = "z")
i_pivot     = input(9, title="Length for peak/valey points", group="High/Low Price Points")

// Definr arrays to store pivot values
var pivotHighValues = array.new_float(0)
var pivotLowValues  = array.new_float(0)

if(i_displayHL and not tfWeekly)
    // Use the function ta.pivothigh/low()
    pivotHigh = ta.pivothigh(high, i_pivot, i_pivot)
    pivotLow  = ta.pivotlow (low,  i_pivot, i_pivot)
    // High Price Point
    if(pivotHigh)
        array.unshift(pivotHighValues, high[i_pivot])
        textHigh9   = i_displayPc ? str.tostring(high[i_pivot], '0.00')+'\n':str.tostring(high[i_pivot], '0.00')
        highestHigh = label.new(bar_index-i_pivot, array.get(pivotHighValues, 0), xloc=xloc.bar_index, yloc=yloc.price, style=label.style_none, text=textHigh9, textcolor=i_colorHL)
    // Low Price Point
    if(pivotLow)
        array.unshift(pivotLowValues, low[i_pivot])    //low[i_pivot]
        textLow9    = "\n" + str.tostring(low[i_pivot], '0.00')
        lowestLow   = label.new(bar_index-i_pivot, array.get(pivotLowValues, 0), xloc=xloc.bar_index, yloc=yloc.price, style=label.style_label_center, text=textLow9, textcolor=i_colorHL, color=color.rgb(0,0,0,100))
    // Percentage Variation
    float pHigh = na
    float pLow  = na
    if array.size(pivotHighValues) > 0
        pHigh := array.get(pivotHighValues, 0)
    if array.size(pivotLowValues) > 0
        pLow  := array.get(pivotLowValues, 0)
    prcVarHigh = (pHigh - pLow)/pLow * 100
    prcVarLow  = (pLow/pHigh - 1) * 100  // Formula to calculate percentage decline
    prcVarHighText = prcVarHigh>=0 ? '+'+str.tostring(prcVarHigh, '0.0') + '%':str.tostring(prcVarHigh, '0.0') + '%'
    prcVarLowText  = prcVarLow>=0 ? '+'+str.tostring(prcVarLow , '0.0') + '%':str.tostring(prcVarLow, '0.0') + '%'
    colorPctUp = prcVarHigh>=0 ? i_colorPctP:i_colorPctN
    colorPctDn = prcVarLow >=0 ? i_colorPctP:i_colorPctN
    // High Price Point Percent Variation 
    if(pivotHigh and i_displayPc)
        pctPivotHigh = na(prcVarHigh)==true ? na:label.new(bar_index-i_pivot, array.get(pivotHighValues, 0), xloc=xloc.bar_index, yloc=yloc.price, style=label.style_none, text=prcVarHighText, textcolor=colorPctUp)
    if(pivotLow and i_displayPc)
        pctPivotLow  = na(prcVarLow)==true ? na:label.new(bar_index-i_pivot, array.get(pivotLowValues, 0), xloc=xloc.bar_index, yloc=yloc.price, style=label.style_label_center, text="\n\n\n" + prcVarLowText, textcolor=colorPctDn, color=color.rgb(0,0,0,100))
```