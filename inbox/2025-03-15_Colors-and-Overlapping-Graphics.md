---
date: 2025-03-15
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Colors and Overlapping Graphics

The default color used for drawing in the `love.draw()` function is white. If you need to change it, you can use the `love.graphics.setColor(red, green, blue)` function.

Each parameter is a number between 0 and 1, where 0 is none of that color and 1 is as much as possible. And use decimal numbers for partial values, like 0.5 for half of the color.

```lua
function love.draw()
  love.graphics.setColor(0, 1, 0) -- set color to green
  love.graphics.rectangle("fill", 200, 400, 200, 100) -- draw a green rectangle
  love.graphics.circle("fill", 300, 200, 100) -- draw a green circle
end
```
You can find that after calling setColor(), all shapes will use this color. If you want different shapes to use different colors, you can call setColor() before each shape to change the color.

```lua
function love.draw()
  love.graphics.setColor(0, 1, 0) -- set color to green
  love.graphics.rectangle("fill", 200, 400, 200, 100) -- draw green rectangle

  love.graphics.setColor(1, 0.63, 0) -- set color to orange
  love.graphics.circle("fill", 300, 200, 100) -- draw orange circle
end
```

If two shapes overlap and two different colors are used.

```lua
function love.draw()
  love.graphics.setColor(0, 1, 0)
  love.graphics.rectangle("fill", 200, 250, 200, 100)
  --                                   ^^^ move up rectangle to overlap circle

  love.graphics.setColor(1, 0.63, 0)
  love.graphics.circle("fill", 300, 200, 100)
end
```

You can see that the orange circle will cover part of the green rectangle, this is because love.graphics.draw() draws in the order of the calls, with the later shapes covering the earlier ones.

Therefore, special attention should be paid to the order of drawing to avoid important things being covered.


## Colors Picker

There are many Color Picker tools that can help you choose colors, but the values of RGB colors are usually from 0 to 255, not from 0 to 1. You can use a simple formula to convert these values:

```lua
function love.draw()
  love.graphics.setColor(255/255, 0/255, 0/255) -- the values from the color picker should be divided by 255
  love.graphics.rectangle("fill", 200, 400, 200, 100)
end
```


