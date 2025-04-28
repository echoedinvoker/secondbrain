---
date: 2025-03-16
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Loading and using images in Love2d: Scrolling background effect

```lua
function love.load()
    background = love.graphics.newImage("background.png")
    bgX = 0
    scrollSpeed = 50
end

function love.update(dt)
    -- Update background position
    bgX = bgX - scrollSpeed * dt
    
    -- Looping background
    if bgX <= -background:getWidth() then
        bgX = 0
    end
end

function love.draw()
    -- Draw two backgrounds to achieve seamless scrolling.
    love.graphics.draw(background, bgX, 0)
    love.graphics.draw(background, bgX + background:getWidth(), 0)
end
```

