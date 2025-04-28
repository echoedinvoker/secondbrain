---
date: 2025-03-19
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 7: Query Colliders

Windfield provides a way to query colliders in a world, it's useful for many senarios.

Here we will use the `queryCircleArea` function to detect all colliders within a 200px radius of the mouse click and destroy them as a demonstration.

```lua
function love.load()
  wf = require("libraries.windfield.windfield")
  world = wf.newWorld(0, 800, false)
  world:setQueryDebugDrawing(true) -- Enable debug drawing for query range

  world:addCollisionClass("Platform")
  world:addCollisionClass("Player")
  world:addCollisionClass("Danger")

  player = world:newRectangleCollider(360, 100, 80, 80, {collision_class = "Player"})
  player:setFixedRotation(true)
  player.speed = 200

  platform = world:newRectangleCollider(200, 400, 400, 40, {collision_class = "Platform"})
  platform:setType("static")

  dangerZone = world:newRectangleCollider(0, 550, 800, 50, {collision_class = "Danger"})
  dangerZone:setType("static")
end

...

-- detect the mouse left click and destroy all colliders within a 200px radius
function love.mousepressed(x, y, button)
  if button == 1 then
    local colliders = world:queryCircleArea(x, y, 200)
    --                     ^^^^^^^^^^^^^^^^ ^^^^  ^^^ radius of the circle
    --                     ^^^^^^^^^^^^^^^^ ^^^^ center of the circle
    --                     ^^^^^^^^^^^^^^^^ this is one of the query functions from Windfield, it returns a table of colliders in the specified area

    -- because the queryCircleArea function returns a table of colliders, we can iterate over it and destroy each collider
    for _,c in ipairs(colliders) do
      c:destroy()
    end
  end
end
```

We can even filter the type of colliders we want to query by passing the fourth argument with the collision class we want to query.

```lua
function love.mousepressed(x, y, button)
  if button == 1 then
    local colliders = world:queryCircleArea(x, y, 200, {"Platform", "Danger"})
    --                                                 ^^^^^^^^^^^^^^^^^^^^^^ it's a table, so you can pass multiple collision classes here
    for _,c in ipairs(colliders) do
      c:destroy()
    end
  end
end
```

Now, the player collider will not be destroyed because it's not in the collision classes we passed to the query function.

