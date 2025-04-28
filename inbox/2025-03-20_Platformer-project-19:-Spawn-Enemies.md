---
date: 2025-03-20
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 19: Spawn Enemies

We want to spawn enemies, and these enemies will move left and right on the platform, and will automatically turn when they reach the edge of the platform.

First, we create a new table `enemies` to store all the enemies and a function `spawnEnemy` to spawn a new enemy and insert it into the `enemies` table.

```sh
 tree
.
├── enemy.lua # new file for the enemies
├── libraries
├── main.lua
├── maps
├── player.lua
└── sprites
```

```lua
-- enemy.lua
enemies = {}

-- function to spawn a new enemy at the given position
function spawnEnemy(x, y)
  local enemy = world:newRectangleCollider(x, y, 70, 90, {collision_class = "Danger"})
  enemy.speed = 200
  table.insert(enemies, enemy)
end

-- iterate over all the enemies and update their position to let them move
function updateEnemies(dt)
  for i,e in ipairs(enemies) do
    local ex, ey = e:getPosition()
    e:setX(ex + e.speed * dt)
  end
end
```

Above, we have established the logic for generating enemies and moving them, but we have not yet implemented the logic for enemies to turn. We can use query colliders to check if enemies have reached the boundaries and then make them turn.

```lua
enemies = {}

function spawnEnemy(x, y)
  local enemy = world:newRectangleCollider(x, y, 70, 90, {collision_class = "Danger"})
  enemy.speed = 200
  enemy.direction = 1 -- add property to store the direction of the enemy, 1 for right and -1 for left
  table.insert(enemies, enemy)
end

function updateEnemies(dt)
  for i,e in ipairs(enemies) do
    local ex, ey = e:getPosition()
    local colliders = world:queryRectangleArea(ex + (40 * e.direction), ey + 40, 10, 10, {'Platform'}) -- query a rectangle area in front of the enemy to check for colliders 'Platform'
    -- if there are no any 'Platform' colliders in front of the enemy, then change the direction
    if #colliders == 0 then
      e.direction = e.direction * -1
    end
    e:setX(ex + e.speed * dt * e.direction)
    --                       ^^^^^^^^^^^^^ multiply the speed with the direction, so the enemy moves in the correct direction
  end
end
```

We have already written the logic for enemies, now we need to call these functions in `main.lua`.

```lua
function love.load()
  ...

  require("player")
  require("enemy") -- include the `enemy.lua` file

  ...

  loadMap()
  spawnEnemy(960, 320) -- spawn an enemy at the given position
end

function love.update(dt)
  world:update(dt)
  gameMap:update(dt)
  playerUpdate(dt)
  updateEnemies(dt) -- call the function to update the enemies to let them move and turn
  cam:lookAt(player:getX(), love.graphics.getHeight() / 2)
end

...

```

Now, when you run the game, you will see the enemies moving left and right on the platform and turning when they reach the edge of the platform.

But if your player collides with the enemy, the error will occur:

```sh
 love .
Error: main.lua:42: calling 'getX' on bad self (Body expected, got nil)
stack traceback:
        [love "boot.lua"]:352: in function <[love "boot.lua"]:348>
        [C]: in function 'getX'
        main.lua:42: in function 'update'
        [love "callbacks.lua"]:162: in function <[love "callbacks.lua"]:144>
        [C]: in function 'xpcall'
```

That's because there are some places in the code where we are trying to access the player object after it has been destroyed. We need to check if the player object exists before accessing it.

```lua
...

function love.update(dt)
  world:update(dt)
  gameMap:update(dt)
  playerUpdate(dt)
  updateEnemies(dt)
  if player.body then -- check if the player object exists
    cam:lookAt(player:getX(), love.graphics.getHeight() / 2)
  end -- close the if statement
end

function love.draw()
  cam:attach()
  gameMap:drawLayer(gameMap.layers["Tile Layer 1"])
  world:draw()
  if player.body then -- check if the player object exists
    playerDraw()
  end -- close the if statement
  cam:detach()
end

...

```
