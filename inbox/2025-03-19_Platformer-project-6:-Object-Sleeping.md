---
date: 2025-03-19
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 6: Object Sleeping

When an object is no longer active, love2d will automatically set it to sleep mode to prevent it from continuing to run physics calculations, saving some computational resources. Once the object is activated by applying a force again, it will wake up and continue to run physics calculations.

However, we move the player by changing player.x instead of using force, so the player will not leave sleep mode, preventing it from falling even when it leaves the platform. We can simply disable sleep mode to solve this problem.

```lua
function love.load()
  wf = require("libraries.windfield.windfield")
  world = wf.newWorld(0, 800, false)
  --                          ^^^^^ disable sleep mode of the world, so that any object in this world will not sleep

  ...
end

...

```

Now, the player will fall when it leaves the platform.

The falling motion includes the rotation and falling speed of the player. If you want to disable the rotation, only the falling speed will be left. You can disable the rotation of the player by adding the following line of code:

```lua
function love.load()
  wf = require("libraries.windfield.windfield")
  world = wf.newWorld(0, 800, false)

  ...

  player = world:newRectangleCollider(360, 100, 80, 80, {collision_class = "Player"})
  player:setFixedRotation(true) -- disable rotation of the player
  player.speed = 200

  ...
end

...

```
