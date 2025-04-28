---
date: 2025-03-16
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Loading and using images in Love2d: Use as background

```lua
function love.load()
    background = love.graphics.newImage("background.png")
end

function love.draw()
    -- Draw the background (before drawing other elements)
    love.graphics.draw(background, 0, 0)
    
    -- Scale the background to fill the screen.
    local scaleX = love.graphics.getWidth() / background:getWidth()
    local scaleY = love.graphics.getHeight() / background:getHeight()
    love.graphics.draw(background, 0, 0, 0, scaleX, scaleY)
    
    -- Other game elements...
end
```

