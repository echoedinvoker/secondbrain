---
date: 2025-03-17
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 5: Let the player face on the mouse cursor

In [[2025-03-17_TopDownShooter-project-3:-Rotate-the-player|TopDownShooter project 3: Rotate the player]] and [[2025-03-17_TopDownShooter-project-4:-Change-the-anchor-position-of-the-player|TopDownShooter project 4: Change the anchor position of the player]], we have learned how to rotate the player and change the anchor position of the player but our final goal is to make the player face on the mouse cursor.

The player and the mouse cursor are two different locations, we need to calculate the radian between them. If you search on the internet, you will find there is a forlua just for this purpose: `atan2(dy, dx)`. `atan2` means arc tangent of two points, `dy` is the difference of the y-axis between two points, `dx` is the difference of the x-axis between two points. The result of `atan2` is the radian between two points.

For readibility, we will create a new function to calculate the radian between two points by using `atan2`, and call this function to get the radian then put it into the parameter of `r` in the `love.graphics.draw` function to make the player face on the mouse cursor.

```lua
function love.load()
  ...

  player = {}
  player.x = love.graphics.getWidth() / 2
  player.y = love.graphics.getHeight() / 2
  player.speed = 180
end

function love.update(dt) ... end

function love.draw()
  ...
  love.graphics.draw(sprites.player, player.x, player.y, playerMouseAngle(), nil, nil, sprites.player:getWidth() / 2, sprites.player:getHeight() / 2)
  --                                                     ^^^^^^^^^^^^^^^^^^ call the function to get the radian between the player and the mouse cursor
  --                                                     ^^^^^^^^^^^^^^^^^^ and put it into the parameter of r to rotate the player to face on the mouse cursor
end

-- create a new function to calculate the radian between two points
function playerMouseAngle()
  return math.atan2(player.y - love.mouse.getY(), player.x - love.mouse.getX())
  --     ^^^^^^^^^^ lua has a built-in function to calculate the arc tangent of two points
end
```

Launch the game, it will be found that the player is always *facing away from* the mouse cursor. The order of point1 and point2 in our math.atan2() should be reversed so that the player faces the mouse cursor.

But a simpler way is to flip the player 180 degrees, that is, add math.pi, so you don't have to change the parameters order of math.atan2().

```lua
...
function love.draw()
  love.graphics.draw(sprites.background, 0, 0)
  love.graphics.draw(sprites.player, player.x, player.y, playerMouseAngle(), nil, nil, sprites.player:getWidth() / 2, sprites.player:getHeight() / 2)
end

function playerMouseAngle()
  return math.atan2(player.y - love.mouse.getY(), player.x - love.mouse.getX()) + math.pi
  --                                                                            ^^^^^^^^^ add math.pi to flip the player 180 degrees
end
```
