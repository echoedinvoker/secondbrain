---
date: 2025-04-08
type: fact
aliases:
  -
hubs:
  - "[[tradingview_pine_script]]"
---

# identify Marubozu and Pinbar candles

```js
//@version=6
indicator("Marubozu and Pinbar Detector", overlay=true)

// 計算蠟燭的各個部分
candleSize = high - low
bodySize = math.abs(close - open)
bodyPercentage = bodySize / candleSize * 100

// Marubozu 條件 (兩種條件之一)
// 1. 蠟燭實體大小 >= 蠟燭大小的 70%
// 2. 蠟燭實體大小 >= 蠟燭大小的 50% 且收盤價距離最高/低價 <= 蠟燭大小的 10%
isMarubozu1 = bodyPercentage >= 70
isMarubozu2 = bodyPercentage >= 50 and 
              ((close > open and (high - close) <= candleSize * 0.1) or 
               (open > close and (close - low) <= candleSize * 0.1))
isMarubozu = isMarubozu1 or isMarubozu2

// Pinbar 條件: 蠟燭實體大小 <= 蠟燭大小的 30%
isPinbar = bodyPercentage <= 30

// 繪製標記
plotshape(isMarubozu, title="Marubozu", location=location.belowbar, 
          color=color.green, style=shape.triangleup, size=size.small)
plotshape(isPinbar, title="Pinbar", location=location.abovebar, 
          color=color.red, style=shape.triangledown, size=size.small)

// 可選：為識別出的蠟燭添加背景色
bgcolor(isMarubozu ? color.new(color.green, 90) : na)
bgcolor(isPinbar ? color.new(color.red, 90) : na)
```

