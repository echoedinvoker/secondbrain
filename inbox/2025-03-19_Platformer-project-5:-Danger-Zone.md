---
date: 2025-03-19
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 5: Danger Zone

In [[2025-03-19_Platformer-project-4:-Collision-Classes|Platformer project 4: Collision Classes]], we've learned how to create a collision class and use it.

Now we want to implement a more practical example. We are going to create a static physics object at the bottom of the entire screen and set its collision class to "Danger". When the Player collides with Danger, the Player will be deleted.

```lua
function love.load()
  wf = require("libraries.windfield.windfield")
  world = wf.newWorld(0, 800)

  world:addCollisionClass("Platform")
  world:addCollisionClass("Player", {ignores = {"Platform"}})
  world:addCollisionClass("Danger") -- Add a new collision class 'Danger'

  player = world:newRectangleCollider(360, 100, 80, 80, {collision_class = "Player"})
  player.speed = 200

  platform = world:newRectangleCollider(200, 400, 400, 40, {collision_class = "Platform"})
  platform:setType("static")

  -- create a new rectangle collider at the bottom of the screen and set its collision class to 'Danger'
  dangerZone = world:newRectangleCollider(0, 550, 800, 50, {collision_class = "Danger"})
  dangerZone:setType("static") -- set the collider to static so it doesn't move
end

function love.update(dt)
  ...

  -- check if the player collides with the 'Danger' collision class
  if player:enter("Danger") then
    player:destroy() -- destroy the player
  end
end

...

```

Launch the game and you will see that when the Player collides with the Danger zone, the error occurs:
```sh
Error: main.lua:22: calling 'getX' on bad self (Body expected, got nil)
stack traceback:
        [love "boot.lua"]:352: in function <[love "boot.lua"]:348>
        [C]: in function 'getX'
        main.lua:22: in function 'update'
        [love "callbacks.lua"]:162: in function <[love "callbacks.lua"]:144>
        [C]: in function 'xpcall'
```

This error occurs because the Player is destroyed, but the update function is still running some code that tries to access the Player's position. Let's fix this issue:

```lua
...

function love.update(dt)
  world:update(dt)

  if player.body then -- wrap the code in a check to see if the player's body exists
    local px = player:getX()
    if love.keyboard.isDown("right") then
      player:setX(px + player.speed * dt)
    end
    if love.keyboard.isDown("left") then
      player:setX(px - player.speed * dt)
    end

    if player:enter("Danger") then
      player:destroy()
    end
  end -- close if
end

...

```

Above, we put the code that accesses the player in `love.update` inside a conditional statement `if player.body then ... end`, so that the related code will not continue to execute after the player is deleted.


