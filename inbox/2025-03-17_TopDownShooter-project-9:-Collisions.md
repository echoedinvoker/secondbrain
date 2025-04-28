---
date: 2025-03-17
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 9: Collisions

We need to determine if the zombie and player collide, we can use the distance between the two to determine. If the distance is very close, it means they have collided.

First, we create a function to calculate the distance between the zombie and player.

```lua
...

-- create a function to calculate the distance between the zombie and player
function distanceToPlayer(x, y)
--                        ^^^^ because there are multiple zombies, zombie position should be arguments of the function
  return math.sqrt((player.x - x)^2 + (player.y - y)^2) -- calculate the distance
  --                ^^^^^^^^ only one player, so we can use the global player variable directly in
end

```

In the final version of the game, once the player collides with a zombie, the game will end. Currently, we just need to make all the zombies disappear.

```lua
function love.load() ... end

function love.update(dt)
  ...

  for _,z in ipairs(zombies) do
    z.x = z.x + math.cos(zombieMouseAngle(z)) * z.speed * dt
    z.y = z.y + math.sin(zombieMouseAngle(z)) * z.speed * dt

    -- use the distanceToPlayer function to get the distance between the zombie and player
    -- if the distance is less than 30, remove all the zombies
    if distanceToPlayer(z.x, z.y) < 30 then
      --                            ^^ you need to test this value to see what works best by yourself
      -- remove all the zombies
      for i,_ in ipairs(zombies) do
        zombies[i] = nil -- in lua, setting a value to nil is equivalent to removing it
      end
    end
  end
end

function love.draw() ... end

...

function distanceToPlayer(x, y) ... end
```
