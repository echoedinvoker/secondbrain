---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 11: Bullets movement

In [[2025-03-18_TopDownShooter-project-10:-Bullets-(spawning-and-drawing)]], we added the ability to spawn bullets. Now we need to make them move.

```lua
function love.load()
  ...

  bullets = {}
end

...

function spawnBullet()
  bullet = {}
  bullet.x = player.x
  bullet.y = player.y
  bullet.direction = playerMouseAngle() -- we've set each bullet's direction to the player's mouse angle when it was spawned
  bullet.speed = 500
  table.insert(bullets, bullet)
end

...

```

So we can use the direction and speed to move the bullets in the `love.update` function.

```lua
...

function love.update(dt)
  ...

  -- iterate through all bullets and move them each frame
  for _,b in ipairs(bullets) do
    b.x = b.x + math.cos(b.direction) * b.speed * dt
    b.y = b.y + math.sin(b.direction) * b.speed * dt
    --          ^^^^^^^^^^^^^^^^^^^^^ use trigonometry to calculate the x and y components of the speed with the direction
  end
end

...

```
