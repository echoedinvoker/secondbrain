---
date: 2025-03-17
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 7: Let zombies face on the player

In [[2025-03-17_TopDownShooter-project-6:-Spawn-Zombies|TopDownShooter project 6: Spawn Zombies]], we have created a script that spawns zombies but they are all facing the right direction. we want to make them face the player.

We have already implemented the functionality of making the player face the mouse cursor in [[2025-03-17_TopDownShooter-project-5:-Let-the-player-face-on-the-mouse-cursor|TopDownShooter project 5: Let the player face on the mouse cursor]], and the method of making the zombie face the player is similar.

```lua
function love.load()
  ...

  player = {}
  player.x = love.graphics.getWidth() / 2
  player.y = love.graphics.getHeight() / 2
  player.speed = 180

  zombies = {}
end

function love.update(dt) ... end

function love.draw()
  love.graphics.draw(sprites.background, 0, 0)
  love.graphics.draw(sprites.player, player.x, player.y, playerMouseAngle(), nil, nil, sprites.player:getWidth() / 2, sprites.player:getHeight() / 2)
  for _,z in ipairs(zombies) do
    love.graphics.draw(sprites.zombie, z.x, z.y)
  end
end

function love.keypressed(key) ... end

-- we've written this function before to calculate the radian angle between the player and the mouse cursor
function playerMouseAngle()
  return math.atan2(player.y - love.mouse.getY(), player.x - love.mouse.getX()) + math.pi
end

-- just copy above function and modify it to calculate the radian angle between the player and the zombie
function zombieMouseAngle(enemy)
--                        ^^^^^ because there are multiple zombies, we need to pass the zombie object as an argument to handle each zombie separately
  return math.atan2(player.y - enemy.y, player.x - enemy.x) + math.pi
  --                           ^^^^^^^             ^^^^^^^
end

function spawnZombie() ... end

```
Now, we have a function that calculates the radian angle between the player and the zombie. Then, we just need to call it at the right place.

```lua
...

function love.draw()
  ...
  for _,z in ipairs(zombies) do
    love.graphics.draw(sprites.zombie, z.x, z.y, zombieMouseAngle(z), nil, nil, sprites.zombie:getWidth() / 2, sprites.zombie:getHeight() / 2)
    --                                           ^^^^^^^^^^^^^^^^^^^            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    --                                           call fn to calc angle           modify the angle position to the center of the sprite to make rotation natural
  end
end

...

```

Lauch the game and spawn some zombies to check if they are facing the player.

We found that all zombies are facing away from the player, because in the function calculating the angle we have a flip of 180 degrees, so we need to remove this flip.

```lua
...
function zombieMouseAngle(enemy)
  return math.atan2(player.y - enemy.y, player.x - enemy.x)
  --                                                        ^^^^ remove the `+ math.pi`
end
...

```
