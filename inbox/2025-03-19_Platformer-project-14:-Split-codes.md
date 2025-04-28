---
date: 2025-03-19
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 14: Split codes

We found that the codes in `main.lua` have become very long, so we want to split the player-related codes into `player.lua`.

```lua
-- main.lua
function love.load()
  anim8 = require("libraries.anim8.anim8")

  sprites = {}
  sprites.playerSheet = love.graphics.newImage("sprites/playerSheet.png")

  local grid = anim8.newGrid(614, 564, sprites.playerSheet:getWidth(), sprites.playerSheet:getHeight())

  animations = {}
  animations.idle = anim8.newAnimation(grid("1-15", 1), 0.05)
  animations.jump = anim8.newAnimation(grid("1-7", 2), 0.05)
  animations.run = anim8.newAnimation(grid("1-15", 3), 0.05)

  wf = require("libraries.windfield.windfield")
  world = wf.newWorld(0, 800, false)
  world:setQueryDebugDrawing(true)

  world:addCollisionClass("Platform")
  world:addCollisionClass("Player")
  world:addCollisionClass("Danger")

  -- player = world:newRectangleCollider(360, 100, 40, 100, {collision_class = "Player"})  -- move the codes related to the player in `love.load` to `player.lua`
  -- player:setFixedRotation(true)
  -- player.animation = animations.idle
  -- player.isMoving = false
  -- player.direction = 1
  -- player.isGrounded = true
  -- player.speed = 200

  platform = world:newRectangleCollider(200, 400, 400, 40, {collision_class = "Platform"})
  platform:setType("static")

  dangerZone = world:newRectangleCollider(0, 550, 800, 50, {collision_class = "Danger"})
  dangerZone:setType("static")
end
```

Move the codes to `player.lua`.

```lua
-- player.lua
player = world:newRectangleCollider(360, 100, 40, 100, {collision_class = "Player"})
player:setFixedRotation(true)
player.animation = animations.idle
player.isMoving = false
player.direction = 1
player.isGrounded = true
player.speed = 200
```

There is also some code related to the player in `love.update` and `love.draw` that needs to be moved to `player.lua`, but it needs to be wrapped in a function.

```lua
-- main.lua
...

function love.update(dt)
  world:update(dt)

  -- if player.body then  -- move the codes related to the player in `love.update` to `player.lua`
  --   local colliders = world:queryRectangleArea(player:getX() - 20, player:getY() + 50, 40, 2, {"Platform"})
  --   if #colliders == 0 then
  --     player.isGrounded = false
  --   else
  --     player.isGrounded = true
  --   end
  --
  --   player.isMoving = false
  --   local px = player:getX()
  --   if love.keyboard.isDown("right") then
  --     player:setX(px + player.speed * dt)
  --     player.isMoving = true
  --     player.direction = 1
  --   end
  --   if love.keyboard.isDown("left") then
  --     player:setX(px - player.speed * dt)
  --     player.isMoving = true
  --     player.direction = -1
  --   end
  --
  --   if player:enter("Danger") then
  --     player:destroy()
  --   end
  -- end
  --
  -- if player.isGrounded then
  --   if player.isMoving then
  --     player.animation = animations.run
  --   else
  --     player.animation = animations.idle
  --   end
  -- else
  --   player.animation = animations.jump
  -- end
  --
  -- player.animation:update(dt)
end

function love.draw()
  world:draw()
  -- local px, py = player:getPosition() -- move the codes related to the player in `love.draw` to `player.lua`
  -- player.animation:draw(sprites.playerSheet, px, py, nil, 0.25 * player.direction, 0.25, 130, 300)
end
...

```

Wrap the codes with functions in `player.lua`.

```lua
-- player.lua
player = world:newRectangleCollider(360, 100, 40, 100, { collision_class = "Player" })
player:setFixedRotation(true)
player.animation = animations.idle
player.isMoving = false
player.direction = 1
player.isGrounded = true
player.speed = 200

-- wrap the codes in `love.update` in a function `playerUpdate`
function playerUpdate(dt)
  if player.body then
    local colliders = world:queryRectangleArea(player:getX() - 20, player:getY() + 50, 40, 2, { "Platform" })
    if #colliders == 0 then
      player.isGrounded = false
    else
      player.isGrounded = true
    end

    player.isMoving = false
    local px = player:getX()
    if love.keyboard.isDown("right") then
      player:setX(px + player.speed * dt)
      player.isMoving = true
      player.direction = 1
    end
    if love.keyboard.isDown("left") then
      player:setX(px - player.speed * dt)
      player.isMoving = true
      player.direction = -1
    end

    if player:enter("Danger") then
      player:destroy()
    end
  end

  if player.isGrounded then
    if player.isMoving then
      player.animation = animations.run
    else
      player.animation = animations.idle
    end
  else
    player.animation = animations.jump
  end

  player.animation:update(dt)
end

-- wrap the codes in `love.draw` in a function `playerDraw`
function playerDraw()
  local px, py = player:getPosition()
  player.animation:draw(sprites.playerSheet, px, py, nil, 0.25 * player.direction, 0.25, 130, 300)
end
```

then, we need to include `player.lua` in `main.lua` and call both functions in `love.update` and `love.draw`.

```lua
-- main.lua
function love.load()
  anim8 = require("libraries.anim8.anim8")

  sprites = {}
  sprites.playerSheet = love.graphics.newImage("sprites/playerSheet.png")

  local grid = anim8.newGrid(614, 564, sprites.playerSheet:getWidth(), sprites.playerSheet:getHeight())

  animations = {}
  animations.idle = anim8.newAnimation(grid("1-15", 1), 0.05)
  animations.jump = anim8.newAnimation(grid("1-7", 2), 0.05)
  animations.run = anim8.newAnimation(grid("1-15", 3), 0.05)

  wf = require("libraries.windfield.windfield")
  world = wf.newWorld(0, 800, false)
  world:setQueryDebugDrawing(true)

  world:addCollisionClass("Platform")
  world:addCollisionClass("Player")
  world:addCollisionClass("Danger")

  require("player")  -- include `player.lua`, this equals to execute all the codes in `player.lua`

  platform = world:newRectangleCollider(200, 400, 400, 40, {collision_class = "Platform"})
  platform:setType("static")

  dangerZone = world:newRectangleCollider(0, 550, 800, 50, {collision_class = "Danger"})
  dangerZone:setType("static")
end

function love.update(dt)
  world:update(dt)
  playerUpdate(dt) -- call `playerUpdate` function
end

function love.draw()
  world:draw()
  playerDraw() -- call `playerDraw` function
end

...

```

Now, the game should work as before, but the codes in `main.lua` are more organized.


