### This Code is a Revised Code from https://www.tradingview.com/u/Steversteves/ 
### Added multiple things to make it work for me and it generates Alertconditions and AlertSignals LONG and SHORT
```
// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © Steversteves
// Some Stuff added by @seatrips
// Also Credits to https://chat.openai.com/
// Inspired By http://www.bitcoinsmartmoney.com/ & https://bitcoin.live/

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
color neutral = color.orange
color standard = color.blue
color fillcolorbull = color.new(color.green, 88)
color fillcolorbear = color.new(color.red, 88)
color fillcolorneutral = color.new(color.gray, 88)

// Determine Colour 

color hicolor = highabove ? bull : bear 
color locolor = lowabove ? bull : bear 
color opcolor = openabove ? bull : bear 
color clcolor = closeabove ? bull : bear 
color lowestcolor = pabelowlow ? neutral : standard
color highestcolor = paabovehigh ? standard : neutral
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
long = not isLong and close > hi and close[1] > hi[1]

//Short
short = not isShort and close < lo and close[1] < lo[1]

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

//SMA & EMA
sma10plot = input(true, title='Show SMA10 on chart')
sma20plot = input(true, title='Show SMA20 on chart')
sma50plot = input(true, title='Show SMA50 on chart')
sma200plot = input(true, title='Show SMA200 on chart')

len10 = input.int(10, minval=1, title='SMA 10 Length')
len20 = input.int(20, minval=1, title='SMA 20 Length')
len50 = input.int(50, minval=1, title='SMA 50 Length')
len200 = input.int(200, minval=1, title='SMA 200 Length')

ema10plot = input(true, title='Show EMA10 on chart')
ema12plot = input(true, title='Show EMA12 on chart')
ema34plot = input(true, title='Show EMA34 on chart')

len8 = input.int(8, minval=1, title='EMA 8 Length')
len12 = input.int(12, minval=1, title='EMA 12 Length')
len34 = input.int(34, minval=1, title='EMA 34 Length')

src = close

//SMA 10
out10 = ta.sma(src, len10)
up10 = out10 > out10[1]
down10 = out10 < out10[1]
mycolor10 = up10 ? color.rgb(3, 48, 2) : down10 ? color.rgb(5, 168, 59) : color.rgb(5, 222, 59)
plot(out10 and sma10plot ? out10 : na, title='SMA10', color=mycolor10, style=plot.style_line, linewidth=2)

//SMA 20
out20 = ta.sma(src, len20)
up20 = out20 > out20[1]
down20 = out20 < out20[1]
mycolor20 = up20 ? color.rgb(117, 8, 81) : down20 ? color.fuchsia : color.rgb(233, 192, 223)
plot(out20 and sma20plot ? out20 : na, title='SMA20', color=mycolor20, style=plot.style_circles, linewidth=2)

//SMA 50
out50 = ta.sma(src, len50)
up50 = out50 > out50[1]
down50 = out50 < out50[1]
mycolor50 = up50 ? color.rgb(77, 36, 3) : down50 ? color.rgb(121, 21, 8) : color.rgb(194, 66, 7, 28)
plot(out50 and sma50plot ? out50 : na, title='SMA50', color=mycolor50, style=plot.style_circles, linewidth=2)

//SMA 200
out200 = ta.sma(src, len200)
up200 = out200 > out200[1]
down200 = out200 < out200[1]
mycolor200 = up200 ? color.rgb(1, 22, 80) : down200 ? color.rgb(41, 63, 161) : color.blue
plot(out200 and sma200plot ? out200 : na, title='SMA200', color=mycolor200, style=plot.style_circles, linewidth=2)

//EMA 8
out8 = ta.ema(src, len10)
up8 = out8 > out8[1]
down8 = out8 < out8[1]
mycolor8 = up8 ? color.rgb(1, 26, 14) : down8 ? color.rgb(6, 138, 179) : color.blue
plot(out8 and ema10plot ? out8 : na, title='EMA8', color=mycolor8, style=plot.style_line, linewidth=2)

//EMA 12
out12 = ta.ema(src, len12)
up12 = out12 > out12[1]
down12 = out12 < out12[1]
mycolor12 = up12 ? color.rgb(4, 167, 77) : down12 ? color.rgb(158, 214, 4) : color.rgb(198, 236, 128)
plot(out12 and ema12plot ? out12 : na, title='EMA12', color=mycolor12, style=plot.style_line, linewidth=2)

//EMA 34
out34 = ta.ema(src, len34)
up34 = out34 > out34[1]
down34 = out34 < out34[1]
mycolor34 = up34 ? color.rgb(126, 69, 3) : down34 ? color.rgb(251, 157, 80) : color.rgb(249, 195, 133)
plot(out34 and ema34plot ? out34 : na, title='EMA34', color=mycolor34, style=plot.style_line, linewidth=2)
```
