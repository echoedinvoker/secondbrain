---
date: 2025-03-22
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 29: Danger Zone again

We have done Danger Zone before, but that was when the map was only the size of a single screen. Now our map has multiple screens size large, so we need to redesign the logic of Danger Zone.

We design the Danger Zone to be a rectangle that is at the bottom outside of the screen. And it should be very wide in order to cover the whole map, it should be wider then the most wide level we have.

```lua
function love.load()
  ...

  dangerZone = world:newRectangleCollider(-500, 800, 5000, 50, {collision_class = "Danger"})
  --                                            ^^^  ^^^^ width of the danger zone, it should be wider then the most wide level we have
  --                                            ^^^ y position of the danger zone, it should be outside of the screen
  dangerZone:setType("static")

  ...
end

...

```

Now we want to let the player spawn at the start point when it touches the Danger Zone. We can use variables to store the start point of the player to make our life easier. Then, rewrite the logic when the player touches the collider with the class "Danger".

```lua
-- player.lua
playerStartX = 360
playerStartY = 100

...

function playerUpdate(dt)
  if player.body then
    ...

    -- logic when the player touches the collider with the class "Danger", it includes the danger zone and enemies
    if player:enter("Danger") then
      player:setX(playerStartX)
      player:setY(playerStartY)
    end
  end

  ...
end

...

```

The design of Danger Zone is basically completed now, but because we set the player's spawn point as a parameter above, many places that originally directly wrote the spawn point with hard code need to be modified.

```lua
-- player.lua
playerStartX = 360
playerStartY = 100

player = world:newRectangleCollider(playerStartX, playerStartY, 40, 100, { collision_class = "Player" })
--                                  ^^^^^^^^^^^^  ^^^^^^^^^^^^
...

```

```lua
-- main.lua
...

function loadMap(mapName)
  saveData.currentLevel = mapName
  love.filesystem.write("data.lua", table.show(saveData, "saveData"))
  destroyAll()
  player:setPosition(playerStartX, playerStartY)
  --                 ^^^^^^^^^^^^  ^^^^^^^^^^^^
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
