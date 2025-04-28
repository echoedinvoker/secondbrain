---
date: 2025-04-08
type: fact
aliases:
  -
hubs:
  - "[[tradingview_pine_script]]"
---

# background color

```pine
// 使用 bgcolor() 函數為圖表添加背景色標記
// 語法: bgcolor(color, title, transp)

// 基本用法 - 當條件符合時顯示背景色
bgcolor(condition ? color.blue : na, title="背景標記")

// 使用透明度 - color.new() 函數的第二個參數控制透明度(0-100)
bgcolor(condition ? color.new(color.blue, 80) : na, title="半透明背景")

// 多條件背景色
// 優先順序從上到下，後面的會覆蓋前面的
bgcolor(condition1 ? color.blue : na, title="條件1")
bgcolor(condition2 ? color.red : na, title="條件2")

// 使用 transp 參數設置透明度(舊方法，建議使用 color.new)
bgcolor(condition ? color.blue : na, title="背景標記", transp=80)
```
