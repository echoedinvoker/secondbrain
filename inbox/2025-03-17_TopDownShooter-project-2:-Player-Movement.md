---
date: 2025-03-17
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 2: Player Movement

In this game, we use WSDA to control the movement of the character. To achieve this goal, we need to use `love.keyboard.isDown` to check if specific keys are pressed, and update the character's position based on the status of the keys.

```lua
function love.load()
  ...

  player = {}
  player.x = love.graphics.getWidth() / 2
  player.y = love.graphics.getHeight() / 2
end

-- Update the player's position based on the key pressed every frame
function love.update(dt)
  if love.keyboard.isDown('s') then
    player.y = player.y + 1
  end
  if love.keyboard.isDown('w') then
    player.y = player.y - 1
  end
  if love.keyboard.isDown('a') then
    player.x = player.x - 1
  end
  if love.keyboard.isDown('d') then
    player.x = player.x + 1
  end
end

function love.draw() ... end
```
You may feel that the movement speed is too slow, so you can change the 1 above to a larger number, such as 3, but we need to change the 1 in various places. A better way is to define this number as a property of a player table, so that the movement speed can be easily adjusted.

```lua
function love.load()
  ...

  player = {}
  player.x = love.graphics.getWidth() / 2
  player.y = love.graphics.getHeight() / 2
  player.speed = 3 -- add new property to define the movement speed of the player
end

function love.update(dt)
  if love.keyboard.isDown('s') then
    player.y = player.y + player.speed
    --                    ^^^^^^^^^^^^
  end
  if love.keyboard.isDown('w') then
    player.y = player.y - player.speed
    --                    ^^^^^^^^^^^^
  end
  if love.keyboard.isDown('a') then
    player.x = player.x - player.speed
    --                    ^^^^^^^^^^^^
  end
  if love.keyboard.isDown('d') then
    player.x = player.x + player.speed
    --                    ^^^^^^^^^^^^
  end
end

function love.draw() ... end
```

However, the frame rate of the game may not be fixed. The average value under normal circumstances is 60FPS, but sometimes it may decrease to 30FPS due to insufficient hardware performance or overly complex game content. At this time, the player's movement speed will be halved. We hope that the player's movement speed is consistent at any frame rate, so we need to multiply the movement speed by the parameter `dt`.

```lua
function love.load()
  ...

  player = {}
  player.x = love.graphics.getWidth() / 2
  player.y = love.graphics.getHeight() / 2
  player.speed = 180
  --             ^^^ The detection rate is usually 60FPS, so dt is usually 1/60, so we need to multiply the original player speed of 3 by 60 to get 180 to maintain the same speed as before.
end

function love.update(dt)
  if love.keyboard.isDown('s') then
    player.y = player.y + player.speed * dt
    --                                 ^^^^
  end
  if love.keyboard.isDown('w') then
    player.y = player.y - player.speed * dt
    --                                 ^^^^
  end
  if love.keyboard.isDown('a') then
    player.x = player.x - player.speed * dt
    --                                 ^^^^
  end
  if love.keyboard.isDown('d') then
    player.x = player.x + player.speed * dt
    --                                 ^^^^
  end
end

function love.draw() ... end
```
