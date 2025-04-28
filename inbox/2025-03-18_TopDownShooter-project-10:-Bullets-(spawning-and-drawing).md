---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 10: Bullets (spawning and drawing)

When the user clicks the left mouse button, generate a bullet and make it fly towards the position of the mouse.

```lua
function love.load()
  ...
  bullets = {} -- create a global table to store all bullets
end

...

function playerMouseAngle()
  return math.atan2(player.y - love.mouse.getY(), player.x - love.mouse.getX()) + math.pi
end

...

-- create a functio to spawn a bullet
function spawnBullet()
  local bullet = {} -- use a local table to store the single bullet information
  -- the bullet will start at the player position
  bullet.x = player.x
  bullet.y = player.y
  bullet.direction = playerMouseAngle() -- the direction is the angle between the player and the mouse in radians
  bullet.speed = 500
  table.insert(bullets, bullet) -- add the bullet to the bullets table
end

...

```

We want to call the `spawnBullet` function when the user clicks the left mouse button. So we can use `love.mousepressed` function to monitor the mouse button press.

```lua
...

-- listen for mouse button press
function love.mousepressed(_, _, button)
  if button == 1 then -- 1 is the left mouse button
    spawnBullet()
  end
end

...

function spawnBullet() ... end

...

```

We can now draw the bullets on the screen.

```lua
...

function love.draw()
  ...

  -- iterate over all bullets and draw them
  for _,b in ipairs(bullets) do
    love.graphics.draw(sprites.bullet, b.x, b.y, nil, nil, nil, sprites.bullet:getWidth() / 2, sprites.bullet:getHeight() / 2)
  end
end

...

```

Test the game and you see that the bullets are too big and overlap the player, we can scale them down by adding a scale factor to the `love.graphics.draw` function and also move the order of it to draw before the player.

```lua
...

function love.draw()
  love.graphics.draw(sprites.background, 0, 0)

  -- move the bullets drawing before the player
  for _,b in ipairs(bullets) do
    love.graphics.draw(sprites.bullet, b.x, b.y, nil, 0.5, nil, sprites.bullet:getWidth() / 2, sprites.bullet:getHeight() / 2)
    --                                                ^^^^^^^^ these two values are the scale factor, we can just give one value to scale uniformly, 0.5 will scale the bullet to half of its size
  end

  love.graphics.draw(sprites.player, player.x, player.y, playerMouseAngle(), nil, nil, sprites.player:getWidth() / 2, sprites.player:getHeight() / 2)
  for _,z in ipairs(zombies) do
    love.graphics.draw(sprites.zombie, z.x, z.y, zombieMouseAngle(z), nil, nil, sprites.zombie:getWidth() / 2, sprites.zombie:getHeight() / 2)
  end
end

...

```
