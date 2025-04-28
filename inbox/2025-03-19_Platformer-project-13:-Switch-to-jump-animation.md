---
date: 2025-03-19
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 13: Switch to jump animation

We want to switch to the jumping animation when the player jumps. The simplest idea is to set `player.animation` to `animations.jump` when the `space` key is pressed.

```lua
function love.load()
  ...
  animations = {}
  animations.idle = anim8.newAnimation(grid("1-15", 1), 0.05)
  animations.jump = anim8.newAnimation(grid("1-7", 2), 0.05)
  animations.run = anim8.newAnimation(grid("1-15", 3), 0.05)
  ...
  player = world:newRectangleCollider(360, 100, 40, 100, {collision_class = "Player"})
  player:setFixedRotation(true)
  player.animation = animations.idle
  player.isMoving = false
  player.direction = 1 -- 1 means right, -1 means left
  player.speed = 200
  ...
end

...

function love.keypressed(key)
  if key == "space" and player and player.body then
    local colliders = world:queryRectangleArea(player:getX() - 20, player:getY() + 50, 40, 2, {"Platform"})
    if #colliders == 0 then return end
    player:applyLinearImpulse(0, -4000)
    player.animation = animations.jump  -- switch to jump animation when the player jumps
  end
end
```

Actual testing will reveal that the player does not display the jump animation even when jumping. This is because the following part of the code will continuously set player.animation to either animations.idle or animations.run:

```lua
...

function love.update(dt)
  ...

  if player.isMoving then
    player.animation = animations.run -- this line will overwrite player.animation to animations.run
  else
    player.animation = animations.idle -- this line will overwrite player.animation to animations.idle
  end

  player.animation:update(dt)
end
...

```
We should let them only work when the player is grounded. So we can add a new property `isGrounded` to the player table to track whether the player is on the ground or not.
We've already implemented the codes to check if the player is on the ground in the `love.keypressed` function. We want to move this part to the `love.update` function to check it continuously.

```lua
function love.load()
  ...
  player = world:newRectangleCollider(360, 100, 40, 100, {collision_class = "Player"})
  player:setFixedRotation(true)
  player.animation = animations.idle
  player.isMoving = false
  player.direction = 1
  player.isGrounded = true  -- add a new property isGrounded to track whether the player is on the ground or not
  player.speed = 200

  ...
end

function love.update(dt)
  world:update(dt)

  if player.body then

    -- check if the player is on the ground by querying the colliders with the "Platform" collision class
    local colliders = world:queryRectangleArea(player:getX() - 20, player:getY() + 50, 40, 2, {"Platform"})
    if #colliders == 0 then
      player.isGrounded = false -- update isGrounded to false if the player is not on the ground
    else
      player.isGrounded = true -- update isGrounded to true if the player is on the ground
    end

    ...
  end

  if player.isGrounded then -- wrap the following code with this condition to only work when the player is on the ground
    if player.isMoving then
      player.animation = animations.run
    else
      player.animation = animations.idle
    end
  else -- add this else block to switch to the jump animation when the player jumps
    player.animation = animations.jump
  end

  player.animation:update(dt)
end

...

function love.keypressed(key)
  if key == "space" and player and player.body then
    -- local colliders = world:queryRectangleArea(player:getX() - 20, player:getY() + 50, 40, 2, {"Platform"}) -- remove this two lines because we've already checked it in the love.update function
    -- if #colliders == 0 then return end
    if player.isGrounded then
      player:applyLinearImpulse(0, -4000)
      -- player.animation = animations.jump -- remove this line because we've already switched to the jump animation in the love.update function
    end
  end
end
```

Now the player will switch to the jump animation when the player jumps and switch back to the idle or run animation when the player lands on the ground.


