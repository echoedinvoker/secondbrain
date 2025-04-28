---
date: 2025-03-16
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# ShootingGallery project 1: set global variables

```lua
-- make all the states needed to be tracked in the game global variables in the function love.load()
function love.load()
  -- use global table to store all the information about the target
  target = {} -- create the table
  target.x = 300 -- use table properties to store the x, y, and radius of the target
  target.y = 300 
  target.radius = 50

  -- other informations like score and timer of the game
  -- they are simple compared to the target, so we can just use global variables to store them
  score = 0
  timer = 0
end

function love.draw()
  -- draw the target
  love.graphics.setColor(1, 0, 0)
  love.graphics.circle("fill", target.x, target.y, target.radius)
  --                           ^^^^^^^^  ^^^^^^^^  ^^^^^^^^^^^^^ instead of specifying the values directly, we get them from the global table
end


