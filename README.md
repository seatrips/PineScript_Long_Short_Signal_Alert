### This Code is a Revised Code from https://www.tradingview.com/u/Steversteves/ 
### Added multiple things to make it work for me and it generates Alertconditions and AlertSignals LONG and SHORT
```
//@version=5
indicator("Final Directional Sentiment S&R EMA SMA rev 4", shorttitle="FDS", overlay=true)

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
color neutral = color.rgb(226, 117, 117)
color standard = color.rgb(142, 240, 210)
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
long = not isLong and close > hi //and close[1] <= hi[1] #remove this and the // for extra confirmation candle

//Short
short = not isShort and close < lo //and close[1] >= lo[1] #remove this and the // for extra confirmation candle

if (long)
    isLong := true
    isShort := false

if (short)
    isLong := false
    isShort := true

//===== End Make alert and signal activate only once=====//


//plot signals on chart after confirmation
plotshape(long and (not confirmOpen or close > open ), color=color.rgb(0, 229, 255), style=shape.arrowup, textcolor=color.blue, title="Buy Signal", text="LONG", location=location.belowbar)
plotshape(short and (not confirmOpen or close < open), color=color.fuchsia, style=shape.arrowdown, textcolor=color.fuchsia, title="Sell Signal", text="SHORT", location=location.abovebar)

//create alerts for buy and sell signals
alertcondition(long and (not confirmOpen or close > open), title="Buy Signal Alert", message="A buy signal has been detected and to be more safe you better wait for next candle confirmation.Check slope of moving averages")
alertcondition(short and (not confirmOpen or close < open), title="Sell Signal Alert", message="A sell signal has been detected and to be more safe you better wait for next candle confirmation.Check slope of moving averages")

// show the buy and sell signals
bgcolor(long and (not confirmOpen or close > open) ? color.new(color.rgb(0, 229, 255), 92) : na, title="background buy confirmed")
bgcolor(short and (not confirmOpen or close < open) ? color.new(color.fuchsia, 92) : na, title="background sell confirmed")

//make labels show up next to lines
src = input(close, title='Source')
showlabels = input(title='Show Labels ?', defval=false)
offset_val = input(title='Label Offset', defval=0)

// Calculate support and resistance levels
resistance = ta.highest(high, 230)
support = ta.lowest(low, 230)

// Plot support and resistance lines
plot(resistance, color=color.new(color.blue, 0), title='resistance', linewidth=2)
plot(support, color=color.new(color.blue, 0), title='support', linewidth=2)

//make labels show up next to lines
plotshape(showlabels ? resistance : na, style=shape.labeldown, location=location.absolute, color=color.blue, textcolor=color.new(color.white, 0), show_last=1, text='resistance', offset=offset_val, title='resistance')
plotshape(showlabels ? support : na, style=shape.labelup, location=location.absolute, color=color.blue, textcolor=color.new(color.white, 0), show_last=1, text='support', offset=offset_val, title='support')

//SMA & EMA
ema10plot = input(true, title='Show EMA10 on chart')
sma20plot = input(true, title='Show SMA20 on chart')
ema34plot = input(true, title='Show EMA34 on chart')
sma50plot = input(true, title='Show SMA50 on chart')
sma200plot = input(true, title='Show SMA200 on chart')

len10 = input.int(10, minval=1, title='EMA 10 Length')
len20 = input.int(20, minval=1, title='SMA 20 Length')
len34 = input.int(34, minval=1, title='EMA 34 Length')
len50 = input.int(50, minval=1, title='SMA 50 Length')
len200 = input.int(200, minval=1, title='SMA 200 Length')

//EMA 10
out10 = ta.sma(src, len10)
up10 = out10 > out10[1]
down10 = out10 < out10[1]
mycolor10 = up10 ? color.rgb(7, 132, 5, 3) : down10 ? color.rgb(5, 168, 59) : color.rgb(5, 222, 59)
plot(out10 and ema10plot ? out10 : na, title='EMA10', color=mycolor10, style=plot.style_line, linewidth=3)

//SMA 20
out20 = ta.sma(src, len20)
up20 = out20 > out20[1]
down20 = out20 < out20[1]
mycolor20 = up20 ? color.rgb(162, 4, 110) : down20 ? color.fuchsia : color.rgb(233, 192, 223)
plot(out20 and sma20plot ? out20 : na, title='SMA20', color=mycolor20, style=plot.style_line, linewidth=3)

//EMA 34
out34 = ta.ema(src, len34)
up34 = out34 > out34[1]
down34 = out34 < out34[1]
mycolor34 = up34 ? color.rgb(183, 87, 8) : down34 ? color.rgb(251, 157, 80) : color.rgb(249, 195, 133)
plot(out34 and ema34plot ? out34 : na, title='EMA34', color=mycolor34, style=plot.style_line, linewidth=3)

//SMA 50
out50 = ta.sma(src, len50)
up50 = out50 > out50[1]
down50 = out50 < out50[1]
mycolor50 = up50 ? color.rgb(155, 72, 5) : down50 ? color.rgb(163, 25, 7, 18) : color.rgb(194, 66, 7, 28)
plot(out50 and sma50plot ? out50 : na, title='SMA50', color=mycolor50, style=plot.style_circles, linewidth=3)

//SMA 200
out200 = ta.sma(src, len200)
up200 = out200 > out200[1]
down200 = out200 < out200[1]
mycolor200 = up200 ? color.rgb(6, 67, 159) : down200 ? color.rgb(61, 85, 192) : color.rgb(101, 133, 237)
plot(out200 and sma200plot ? out200 : na, title='SMA200', color=mycolor200, style=plot.style_circles, linewidth=3)

```
