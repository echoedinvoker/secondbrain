---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 18: boundaries of player movement

In [[2025-03-17_TopDownShooter-project-2:-Player-Movement|TopDownShooter project 2: Player Movement]], we created a player movement script that allows the player to move in all directions. However, the player can move off the screen. So we want to add boundaries to the player movement so that the player cannot move off the screen.

```lua
...

function love.update(dt)
  if gameState == 2 then
    -- besides detecting the pressed keys, we also need to check if the player is within the boundaries
    if love.keyboard.isDown('s') and player.y < love.graphics.getHeight() then
    --                           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ add the boundary here
      player.y = player.y + player.speed * dt
    end
    if love.keyboard.isDown('w') and player.y > 0 then
    --                           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ add the boundary here
      player.y = player.y - player.speed * dt
    end
    if love.keyboard.isDown('a') and player.x > 0 then
    --                           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ add the boundary here
      player.x = player.x - player.speed * dt
    end
    if love.keyboard.isDown('d') and player.x < love.graphics.getWidth() then
    --                           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ add the boundary here
      player.x = player.x + player.speed * dt
    end
  end
  ...
end

...

```
