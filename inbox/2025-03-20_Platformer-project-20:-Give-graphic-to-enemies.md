---
date: 2025-03-20
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 20: Give graphic to enemies

In [[2025-03-20_Platformer-project-19:-Spawn-Enemies|Platformer project 19: Spawn Enemies]], we've spawned enemies but only rectangle objects. This time, we'll give them a graphic.

```sh
 tree
.
├── enemy.lua
├── libraries
├── main.lua
├── maps
├── player.lua
└── sprites
    ├── enemySheet.png  # downloaded enemy sheet
    └── playerSheet.png
```

Same as the player, we can load the enemy sprite sheet and create an animation for it with anim8 library.
`enemySheet.png` is 200x79 pixels and contains 2 frames of 100x79 pixels each.

```lua
function love.load()
  ...

  sprites = {}
  sprites.playerSheet = love.graphics.newImage("sprites/playerSheet.png")
  sprites.enemySheet = love.graphics.newImage("sprites/enemySheet.png") -- load enemy sprite sheet

  local grid = anim8.newGrid(614, 564, sprites.playerSheet:getWidth(), sprites.playerSheet:getHeight())
  local enemyGrid = aim8.newGrid(100, 79, sprites.enemySheet:getWidth(), sprites.enemySheet:getHeight()) -- create grid for enemy sprite sheet

  animations = {}
  animations.idle = anim8.newAnimation(grid("1-15", 1), 0.05)
  animations.jump = anim8.newAnimation(grid("1-7", 2), 0.05)
  animations.run = anim8.newAnimation(grid("1-15", 3), 0.05)
  animations.enemy = anim8.newAnimation(enemyGrid("1-2", 1), 0.03) -- create animation for enemy

  ...

end

...

```

Similar to the player, we can create a property `animation` in the `enemy` table to store the animation we created above and set its update and draw functions in `enemy.lua`.

```lua
-- enemy.lua
enemies = {}

function spawnEnemy(x, y)
  local enemy = world:newRectangleCollider(x, y, 70, 90, {collision_class = "Danger"})
  enemy.speed = 200
  enemy.direction = 1
  enemy.animation = animations.enemy  -- create animation property for enemy animation
  table.insert(enemies, enemy)
end

function updateEnemies(dt)
  for i,e in ipairs(enemies) do
    e.animation:update(dt) -- update enemy animation
    local ex, ey = e:getPosition()
    local colliders = world:queryRectangleArea(ex + (40 * e.direction), ey + 40, 10, 10, {'Platform'})
    if #colliders == 0 then
      e.direction = e.direction * -1
    end
    e:setX(ex + e.speed * dt * e.direction)
  end
end

-- create a new function to draw the animation of all enemies
function drawEnemies()
  for i,e in ipairs(enemies) do
    local ex, ey = e:getPosition()
    e.animation:draw(sprites.enemySheet, ex, ey, nil, e.direction, 1, 50, 65)
    --                                                                    ^^ instead of 90/2, we lift the enemy sprite up by 25 pixels because this enemy is a bit floating
  end
end
```

Remember to call the new function `drawEnemies()` in `love.draw()` in `main.lua`.

```lua
-- main.lua
...

function love.draw()
  cam:attach()
  gameMap:drawLayer(gameMap.layers["Tile Layer 1"])
  world:draw()
  if player.body then
    playerDraw()
  end
  drawEnemies() -- draw enemies
  cam:detach()
end

...

```
