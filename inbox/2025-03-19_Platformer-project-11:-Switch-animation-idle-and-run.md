---
date: 2025-03-19
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 11: Switch animation idle and run

The animation of the player must switch between idle and run animations based on whether the player is moving. We add a property `isMoving` to the player table to track if the player is moving.

```lua
function love.load()
  ...

  player = world:newRectangleCollider(360, 100, 40, 100, {collision_class = "Player"})
  player:setFixedRotation(true)
  player.animation = animations.idle
  player.isMoving = false -- Add this property to track if the player is moving
  player.speed = 200

  ...
end

...

```

Then, we need to update it when the player moves, which equals to the 'left' or 'right' key is pressed.

```lua
function love.load()
  ...

  player = world:newRectangleCollider(360, 100, 40, 100, {collision_class = "Player"})
  player:setFixedRotation(true)
  player.animation = animations.idle
  player.isMoving = false
  player.speed = 200

  ...
end

function love.update(dt)
  world:update(dt)

  if player.body then
    local px = player:getX()
    if love.keyboard.isDown("right") then
      player:setX(px + player.speed * dt)
      player.isMoving = true -- Set isMoving to true when the player is moving
    end
    if love.keyboard.isDown("left") then
      player:setX(px - player.speed * dt)
      player.isMoving = true -- Set isMoving to true when the player is moving
    end

    if player:enter("Danger") then
      player:destroy()
    end
  end
  player.animation:update(dt)
end

...

```

Now, we can change the animation of the player from idle to run based on the `isMoving` property.

```lua
...

function love.update(dt)
  world:update(dt)

  if player.body then
    local px = player:getX()
    if love.keyboard.isDown("right") then
      player:setX(px + player.speed * dt)
      player.isMoving = true
    end
    if love.keyboard.isDown("left") then
      player:setX(px - player.speed * dt)
      player.isMoving = true
    end

    if player:enter("Danger") then
      player:destroy()
    end
  end

  -- update the animation of the player based on the isMoving property, this should be written before player.animation:update(dt)
  if player.isMoving then
    player.animation = animations.run
  else
    player.animation = animations.idle
  end

  player.animation:update(dt)
end

...

```

Now, the player's animation will switch to the run animation when the player is moving but we never set `player.isMoving` to false when the player stops moving. We can set `player.isMoving` to false at the start of the `love.update` function to make it work.

```lua
...

function love.update(dt)
  world:update(dt)

  if player.body then
    player.isMoving = false -- Set isMoving to false at the start of the update function, if the player is not moving, it will be still false when reaching the animation update line
    local px = player:getX()
    if love.keyboard.isDown("right") then
      player:setX(px + player.speed * dt)
      player.isMoving = true
    end
    if love.keyboard.isDown("left") then
      player:setX(px - player.speed * dt)
      player.isMoving = true
    end

    if player:enter("Danger") then
      player:destroy()
    end
  end

  if player.isMoving then
    player.animation = animations.run
  else
    player.animation = animations.idle
  end

  player.animation:update(dt)
end

...

```

Now, the player's animation will switch back to the idle animation when the player stops moving.

