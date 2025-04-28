---
date: 2025-03-19
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 12: Player animation direction

If the `scale` parameter in `player.animation.draw` is a complex number, the image will be flipped.

We add a property `direction` to the player table to track the direction of the character, update it when the character is moving left or right, then multiply the `sx` value by the direction of the character to flip the image when the character based on the player's direction.

```lua
function love.load()
  ...

  player = world:newRectangleCollider(360, 100, 40, 100, {collision_class = "Player"})
  player:setFixedRotation(true)
  player.animation = animations.idle
  player.isMoving = false
  player.direction = 1 -- add this property to track the direction of the character, 1 is right, -1 is left
  player.speed = 200
  ...
end

function love.update(dt)
  world:update(dt)

  if player.body then
    player.isMoving = false
    local px = player:getX()
    if love.keyboard.isDown("right") then
      player:setX(px + player.speed * dt)
      player.isMoving = true
      player.direction = 1 -- update the direction of the character when moving right
    end
    if love.keyboard.isDown("left") then
      player:setX(px - player.speed * dt)
      player.isMoving = true
      player.direction = -1 -- update the direction of the character when moving left
    end

    if player:enter("Danger") then
      player:destroy()
    end
  end
  ...
end

function love.draw()
  world:draw()
  local px, py = player:getPosition()
  player.animation:draw(sprites.playerSheet, px, py, nil, 0.25 * player.direction, 0.25, 130, 300)
  --                                                          ^^^^^^^^^^^^^^^^^^^  ^^^^ if sy is nil, it'll inherit the sx value, but we don't want the animation to be flipped vertically, so we set it to 0.25 instead of nil
  --                                                          ^^^^^^^^^^^^^^^^^^^ multiply the sx value by the direction of the character, so the image will be flipped when the character is moving left
end

...

```
