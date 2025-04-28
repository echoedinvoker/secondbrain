---
date: 2025-03-15
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Draw Shapes

In the `love.graphics` library, there are many functions that can be used to draw different simple shapes.

Note that `love.graphics` can only be used in the `love.draw()` function.

We can find all the `love.graphics` functions at [https://love2d.org/wiki/love.graphics](https://love2d.org/wiki/love.graphics).

Here we use the `love.graphics.rectangle()` to draw a rectangle as an example.

```lua
function love.draw()
  love.graphics.rectangle("fill", 100, 100, 200, 150)
  --           ^^^^^^^^^^ ^^^^^^  ^^^  ^^^  ^^^  ^^^
  --                       mode    x    y  width height
end
```

mode can be `"fill"` or `"line"`. `"fill"` will fill the rectangle with the current color, and `"line"` will draw the outline of the rectangle.

x and y are the coordinates of the top-left corner of the rectangle.

width and height are the width and height of the rectangle.


