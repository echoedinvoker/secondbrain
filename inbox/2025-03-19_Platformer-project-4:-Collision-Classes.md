---
date: 2025-03-19
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 4: Collision Classes

How to determine the collision between the player and what object? This is a very important question because we do not want the player to collide with the floor and enemies in the same way. This problem can be solved through collision classes.

We can create a new collision class by using the `world:addCollisionClass(<collision class name>)` function. Then assign the collision class to the physics object by argument.

```lua
function love.load()
  wf = require("libraries.windfield.windfield")
  world = wf.newWorld(0, 800)

  -- create collision classes
  world:addCollisionClass("Player")
  world:addCollisionClass("Platform")

  player = world:newRectangleCollider(360, 100, 80, 80, {collision_class = "Player"})
  --                                                    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ assign the collision class
  player.speed = 200

  platform = world:newRectangleCollider(200, 400, 400, 40, {collision_class = "Platform"})
  --                                                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ assign the collision class
  platform:setType("static")
end

...

```

Next, we can set the collision behavior between different collision classes as follows:

```lua
function love.load()
  wf = require("libraries.windfield.windfield")
  world = wf.newWorld(0, 800)

  world:addCollisionClass("Platform")  -- this collision class is referred in the player collision class, so it should be defined first
  world:addCollisionClass("Player", {ignores = {"Platform"}})
  --                                ^^^^^^^^^^^^^^^^^^^^^^^^ when the player collides with the platform, it will ignore the collision

  player = world:newRectangleCollider(360, 100, 80, 80, {collision_class = "Player"})
  player.speed = 200

  platform = world:newRectangleCollider(200, 400, 400, 40, {collision_class = "Platform"})
  platform:setType("static")
end

...

```

Launch the game and you will see that the player will not collide with the platform but pass through it.
