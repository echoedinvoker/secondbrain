---
date: 2025-03-17
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 6: Spawn Zombies

Zombies, like players, have attributes such as sprite, x, y, and speed, and also need to consider how to move and turn. However, the biggest difference from players is that there is only one player, while there can be many zombies.

In order to handle information for multiple zombies, we will create a table for each zombie to store informations of each one, and then create a table to store all these tables.

```lua
function love.load()
  ...

  player = {}
  player.x = love.graphics.getWidth() / 2
  player.y = love.graphics.getHeight() / 2
  player.speed = 180

  zombies = {}  -- create a table to store all zombies
end

function love.update(dt) ... end

function love.draw() ... end

function playerMouseAngle() ... end

-- create a function to spawn a zombie and insert it into the table `zombies`
function spawnZombie()
  -- create a table for a single zombie
  local zombie = {}
--^^^^^ this table should be local, but we can still access it after it is inserted into the table `zombies`
  zombie.x = math.random(0, love.graphics.getWidth())
  zombie.y = math.random(0, love.graphics.getHeight()) -- let the zombie spawn at a random position inside the window
  zombie.speed = 100
  table.insert(zombies, zombie) -- insert the zombie into the table `zombies`
end
```
We have created a function above to spawn zombies and insert them into the table `zombies`, but we have not written the code to call this function yet.

For testing purposes, we temporarily allow the function `spawnZombie()` to be called when the user presses the `space` key.

```lua
function love.load()
  ...

  zombies = {}
end

function love.update(dt) ... end

function love.draw()
  love.graphics.draw(sprites.background, 0, 0)
  love.graphics.draw(sprites.player, player.x, player.y, playerMouseAngle(), nil, nil, sprites.player:getWidth() / 2, sprites.player:getHeight() / 2)
end

-- function `love.keypressed` is used to monitor the key pressed event
function love.keypressed(key)
--                       ^^^ it receives the pressed key as an argument, which is a string
  -- when pressed the `space` key, spawn a zombie
  if key == 'space' then
    spawnZombie()
  end
end

function playerMouseAngle() ... end

function spawnZombie()
  local zombie = {}
  zombie.x = math.random(0, love.graphics.getWidth())
  zombie.y = math.random(0, love.graphics.getHeight())
  zombie.speed = 100
  table.insert(zombies, zombie)
end
```

We have set up a mechanism above to trigger the `spawnZombie()` function, but even though zombies are generated, we still can't see them on the screen because we haven't written the code to draw the zombies.

We need to consider that there will be multiple zombies in the table `zombies` that need to be drawn, so we must iterate through the table `zombies` to draw each zombie.

```lua
function love.load() ... end

function love.update(dt) ... end

function love.draw()
  love.graphics.draw(sprites.background, 0, 0)
  love.graphics.draw(sprites.player, player.x, player.y, playerMouseAngle(), nil, nil, sprites.player:getWidth() / 2, sprites.player:getHeight() / 2)

  -- iterating the table `zombies` to draw each zombie
  for _,z in ipairs(zombies) do
    love.graphics.draw(sprites.zombie, z.x, z.y)
  end
end

...

```

Now we can spawn zombies by pressing the `space` key, and we can see them on the screen at random positions.


