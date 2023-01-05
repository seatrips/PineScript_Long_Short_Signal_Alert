## Original Script By https://www.tradingview.com/u/Steversteves/
```
// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © Steversteves

//@version=5
indicator("Directional Sentiment Line", overlay=true)

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

color bull = color.green 
color bear = color.red 
color neutral = color.orange
color standard = color.blue
color fillcolorbull = color.new(color.green, 50)
color fillcolorbear = color.new(color.red, 50)
color fillcolorneutral = color.new(color.gray, 50)

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

// Fills
fill(hir, lor, color = fillfinal, editable=false)

// Band Plots 
plot(lowest, "Lowest Target", color = lowestcolor, linewidth=3)
plot(highest, "Highest Target", color = highestcolor, linewidth=3)

