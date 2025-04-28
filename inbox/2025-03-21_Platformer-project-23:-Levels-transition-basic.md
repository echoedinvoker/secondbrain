---
date: 2025-03-21
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 23: Levels transition basic

In [[2025-03-21_Platformer-project-22:-Create-another-level-by-Tiled|Platformer project 22: Create another level by Tiled]], we created a new level and modified the code to load it. Now, we want to switch between levels by function call.

```lua
function love.load()
  ...

  loadMap('level1')
  --      ^^^^^^^^ load specific map by its name
end

...

function love.keypressed(key)
  ...

  -- we want to switch between levels when the grame is running, we let the player press 'r' to reload the different map for the testing purpose
  if key == "r" then
    loadMap('level2')
  end
end

function loadMap(mapName)
--               ^^^^^^^ instead of hardcoding the map name, we pass it as an argument
  gameMap = sti("maps/" .. mapName .. ".lua")
  --                   ^^^^^^^^^^^^^^ so we can load different maps by their names now
  for _, obj in pairs(gameMap.layers["Object Layer 1"].objects) do
    spawnPlatform(obj.x, obj.y, obj.width, obj.height)
  end
  for _, obj in pairs(gameMap.layers["Object Layer 2"].objects) do
    spawnEnemy(obj.x, obj.y)
  end
end
```

Start the game and try pressing the `r` key, you will see the map switch to `level2`, but in `level1`, all objects except tiles still exist, we need to write additional codes to clear the old objects.

```lua
...

-- create a function to destroy all objects (excluding player)
function destroyAll()
  for i, platform in ipairs(platforms) do
    platform:destroy()
  end
  platforms = {}
  for i, enemy in ipairs(enemies) do
    enemy.body:destroy()
  end
  enemies = {}
end

...

function loadMap(mapName)
  destroyAll() -- destroy all objects before loading the new map
  gameMap = sti("maps/" .. mapName .. ".lua")
  for _, obj in pairs(gameMap.layers["Object Layer 1"].objects) do
    spawnPlatform(obj.x, obj.y, obj.width, obj.height)
  end
  for _, obj in pairs(gameMap.layers["Object Layer 2"].objects) do
    spawnEnemy(obj.x, obj.y)
  end
end

```

Then, you can switch between levels without any problem. 

