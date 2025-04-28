---
date: 2025-03-16
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Loading and using images in Love2d: Basic Usage

```lua
function love.load()
    -- Load image
    myImage = love.graphics.newImage("path/to/image.png")
end

function love.draw()
    -- Draw image (parameters: image, x coordinate, y coordinate, rotation angle, x scale, y scale)
    love.graphics.draw(myImage, 100, 100)
end
```
