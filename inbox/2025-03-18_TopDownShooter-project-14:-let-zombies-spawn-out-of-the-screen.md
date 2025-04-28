---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 14: let zombies spawn out of the screen

In [[2025-03-17_TopDownShooter-project-6:-Spawn-Zombies|TopDownShooter project 6: Spawn Zombies]], we created a script that spawns zombies at random positions on the screen. However, the zombies appear out of nowhere, which doesn't look very realistic. We want to make it look like the zombies are coming from outside the screen.

```lua
...

function spawnZombie()
  local zombie = {}

  -- before, we were spawning zombies at random positions on the screen
  zombie.x = math.random(0, love.graphics.getWidth())
  zombie.y = math.random(0, love.graphics.getHeight())

  zombie.speed = 100
  zombie.dead = false
  table.insert(zombies, zombie)
end

```

```lua
...

function spawnZombie()
  local zombie = {}

  -- set the zombie's initial position to 0, we'll adjust it later
  zombie.x = 0
  zombie.y = 0

  zombie.speed = 100
  zombie.dead = false
  table.insert(zombies, zombie)
end

```
We first consider from which direction outside the screen the zombie will enter.
```lua
...

function spawnZombie()
  local zombie = {}
  zombie.x = 0
  zombie.y = 0
  zombie.speed = 100
  zombie.dead = false

  local side = math.random(1, 4) -- 1: top, 2: right, 3: bottom, 4: left

  table.insert(zombies, zombie)
end

```
As above, we added a local variable `side` to randomly determine from which direction to enter the screen. Next, we will set the initial position of the `zombie` based on the value of `side`.
```lua
...

function spawnZombie()
  local zombie = {}
  zombie.x = 0
  zombie.y = 0
  zombie.speed = 100
  zombie.dead = false

  local side = math.random(1, 4) -- 1: top, 2: right, 3: bottom, 4: left
  -- set the zombie's initial position based on the side
  if side == 1 then
    zombie.x = math.random(0, love.graphics.getWidth())
    zombie.y = -30
  elseif side == 2 then
    zombie.x = love.graphics.getWidth() + 30
    zombie.y = math.random(0, love.graphics.getHeight())
  elseif side == 3 then
    zombie.x = math.random(0, love.graphics.getWidth())
    zombie.y = love.graphics.getHeight() + 30
  elseif side == 4 then
    zombie.x = -30
    zombie.y = math.random(0, love.graphics.getHeight())
  end

  table.insert(zombies, zombie)
end

```

Now, the zombies will spawn from outside the screen. The `side` variable determines from which direction the zombie will enter the screen, and the `if` statement sets the initial position of the zombie accordingly. The `zombie` will then move towards the player, creating a more realistic spawning effect.
