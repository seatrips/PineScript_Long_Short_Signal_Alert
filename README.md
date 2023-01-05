### This Code is a Revised Code from https://www.tradingview.com/u/Steversteves/ 
### Added multiple things to make it work for me and it generates Alertconditions and AlertSignals LONG and SHORT
```
// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © Steversteves
// Some Stuff added by @seatrips

//@version=5
indicator("Directional Sentiment Line rev 3", overlay=true)

lengthInput = input.int(150, "Length", minval = 2)

// Determine variables 

hi = ta.sma(high, lengthInput)
lo = ta.sma(low, lengthInput)
cl = ta.sma(close, lengthInput) 
op = ta.sma(open, lengthInput) 

// Lowest vs highest

lowest = ta.lowest(lo, lengthInput)
highest = ta.highest(hi, lengthInput)

// bools for ribbon 

bool closeabove = close >= cl 
bool closebelow = close <= cl 
bool highabove = high >= hi 
bool lowabove = low >= lo 
bool openabove = open >= op 

// bools for highest/lowest 

bool paabovehigh = close > highest 
bool pabelowlow = close < lowest 
bool middle = close >= lo and close <= hi

// Colours 

color bull = color.new(color.green, 50)
color bear = color.new(color.red, 50)
color neutral = color.rgb(43, 42, 42)
color standard = color.rgb(73, 74, 75)
color fillcolorbull = color.new(color.green, 88)
color fillcolorbear = color.new(color.red, 88)
color fillcolorneutral = color.new(color.gray, 88)

// Determine Colour 

color hicolor = highabove ? bull : bear 
color locolor = lowabove ? bull : bear 
color opcolor = openabove ? bull : bear 
color clcolor = closeabove ? bull : bear 
color lowestcolor = pabelowlow ? neutral : standard
color highestcolor = paabovehigh ? neutral : standard 
color fillfinal = closeabove ? fillcolorbull : closebelow ? fillcolorbear : middle ? fillcolorneutral : na 

//Ribbon Plots
hir = plot(hi, color = hicolor)
lor = plot(lo, color = locolor)
plot(cl, color = clcolor)
plot(op, color = opcolor)

// Determine Colour 
color candlecolor = closeabove ? bull : closebelow ? bear : middle ? neutral : na

// Set candle colors
barcolor(candlecolor)

// Fills
fill(hir, lor, color = fillfinal, editable=true)

// Band Plots 
plot(lowest, "Lowest Target", color = lowestcolor, linewidth=4)
plot(highest, "Highest Target", color = highestcolor, linewidth=4)

confirmOpen = input(true, title="Require Open above/below Close for Confirmation")

//===== start Make alert and signal activate only once =====//
var isLong = false
var isShort = false

//Long
long = not isLong and close > hi and close[1] <= hi[1]

//Short
short = not isShort and close < lo and close[1] >= lo[1]

if (long)
    isLong := true
    isShort := false

if (short)
    isLong := false
    isShort := true

//===== End Make alert and signal activate only once=====//


//plot signals on chart after confirmation
plotshape(long and (not confirmOpen or close > open ), color=color.yellow, style=shape.arrowup, text="LONG", textcolor=color.yellow, title="Buy Signal", location=location.belowbar)
plotshape(short and (not confirmOpen or close < open), color=color.yellow, style=shape.arrowdown, text="SHORT", textcolor=color.yellow, title="Sell Signal", location=location.abovebar)

//create alerts for buy and sell signals
alertcondition(long and (not confirmOpen or close > open), title="Buy Signal Alert", message="A buy signal has been detected and to be more safe you better wait for next candle confirmation.Check slope of moving averages")
alertcondition(short and (not confirmOpen or close < open), title="Sell Signal Alert", message="A sell signal has been detected and to be more safe you better wait for next candle confirmation.Check slope of moving averages")

// show the buy and sell signals
bgcolor(long and (not confirmOpen or close > open) ? color.new(color.green, 92) : na, title="background buy confirmed")
bgcolor(short and (not confirmOpen or close < open) ? color.new(color.red, 92) : na, title="background sell confirmed")
```
