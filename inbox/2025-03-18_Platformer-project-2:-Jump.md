---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 2: Jump

In [[2025-03-18_Platformer-project-1:-Physics|Platformer project 1: Physics]], we created a rectangle entity as our player. Now, we will add the ability to jump.

Because `world:newRectangleCollider` creates a entity including body, shape and fixture properties, we can check the love2d wiki to see what functions of these properties can be used to make the player jump.

In the [love.physics](https://love2d.org/wiki/love.physics), we see that the link to the [Body](https://love2d.org/wiki/Body) page where we can find the `applyLinearImpulse` function. This function can be used to apply an impulse to the body, which is what we need to make the player jump.

```lua
function love.load()
  wf = require("libraries.windfield.windfield")
  world = wf.newWorld(0, 800)
  --                     ^^^ adjust the gravity to control the drop speed
  player = world:newRectangleCollider(360, 100, 80, 80)
  platform = world:newRectangleCollider(200, 400, 400, 40)
  platform:setType("kinematic")
end

function love.update(dt)
  world:update(dt)
end

function love.draw()
  world:draw()
end

-- use `love.keypressed` to detect the key press event
function love.keypressed(key)
  if key == "space" then -- if we want to make the player jump when the space key is pressed
    player:applyLinearImpulse(0, -7000) -- apply an impulse to the player's body to make it jump up
    --                        ^  ^^^^^ impulse value on y-axis, adjust this value to control the jump height
    --                        ^ impulse value on x-axis
  end
end
```
