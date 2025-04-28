---
date: 2025-03-21
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 25: Switch multiple levels by current name

In [[2025-03-21_Platformer-project-24:-Levels-transition-by-flags|Platformer project 24: Levels transition by flags]], we learned how to switch levels by flags but still use hard-coded level names. In this tutorial, we will learn how to switch the correct level by the current level name.

```lua
function love.load()
  ...

  flag = {}
  flag.x = 0
  flag.y = 0

  currentLevel = "level1"  -- create a global variable to track the current level name

  loadMap(currentLevel)
  --      ^^^^^^^^^^^^ instead of hard-coded level name, use the current level name to load the map
end

function love.update(dt)
  world:update(dt)
  gameMap:update(dt)
  playerUpdate(dt)
  updateEnemies(dt)
  if player.body then
    cam:lookAt(player:getX(), love.graphics.getHeight() / 2)
  end

  local colliders = world:queryCircleArea(flag.x, flag.y, 10, {"Player"})
  if #colliders > 0 then
    -- based on the current level name, switch to the other level
    if currentLevel == "level1" then
      loadMap('level2')
    else
      loadMap('level1')
    end
  end
end

function loadMap(mapName)
  currentLevel = mapName -- whenever we load a new map, update the current level name
  destroyAll()
  player:setPosition(300, 100)
  gameMap = sti("maps/" .. mapName .. ".lua")
  for _, obj in pairs(gameMap.layers["Object Layer 1"].objects) do
    spawnPlatform(obj.x, obj.y, obj.width, obj.height)
  end
  for _, obj in pairs(gameMap.layers["Object Layer 2"].objects) do
    spawnEnemy(obj.x, obj.y)
  end
  for _, obj in pairs(gameMap.layers["Flag"].objects) do
    flag.x = obj.x
    flag.y = obj.y
  end
end
```

In the above codes, we create a global variable `currentLevel` to continuously track the current level name, so that when switching, we can set conditions based on the current level name to switch to the correct level. (In this case, we only have two levels, so we can use a simple `if` statement to switch between them.)


