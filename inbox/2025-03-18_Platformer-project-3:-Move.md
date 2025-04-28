---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 3: Move

We want to allow our player to move left and right using the arrow keys. We choose to mutate the player's x coordinate directly because it's the simplest way to move the player. But we need to know how to extract the player's x coordinate from the physics entity and how to set the player's x coordinate into it.

We can use `body:getX` to get the current x coordinate of the player to determine the position of the next frame, and then use `body:setX` to set the player's x coordinate.

```lua
function love.load()
  ...

  player = world:newRectangleCollider(360, 100, 80, 80)
  player.speed = 200 -- add speed property to player, it's allowed because `world:newRectangleCollider` returns a table

  ...
end

function love.update(dt)
  world:update(dt)

  -- move player
  local px = player:getX() -- get the current x coordinate of the player with `body:getX`
  if love.keyboard.isDown("right") then
    player:setX(px + player.speed * dt) -- set the player's x coordinate `body:setX`
  end
  if love.keyboard.isDown("left") then
    player:setX(px - player.speed * dt) -- set the player's x coordinate `body:setX`
  end
end

...

```
