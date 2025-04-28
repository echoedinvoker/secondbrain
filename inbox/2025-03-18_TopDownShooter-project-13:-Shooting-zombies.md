---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 13: Shooting zombies

So far, we have created the functionality for bullet generation and movement, but the bullets will pass through zombies without any effect. We need to add some functionality to detect if the bullet has hit a zombie, and delete the bullet and the hit zombie when it hits.

```lua
function love.load()
  ...

  zombies = {}
  bullets = {}
end

...

function spawnZombie()
  local zombie = {}
  zombie.x = math.random(0, love.graphics.getWidth())
  zombie.y = math.random(0, love.graphics.getHeight())
  zombie.speed = 100
  zombie.dead = false -- Add a dead flag to the zombie to check if it is dead
  table.insert(zombies, zombie)
end

function spawnBullet()
  bullet = {}
  bullet.x = player.x
  bullet.y = player.y
  bullet.direction = playerMouseAngle()
  bullet.speed = 500
  bullet.dead = false -- Add a dead flag to the bullet to check if it is dead
  table.insert(bullets, bullet)
end

...

```

```lua
...

function love.update(dt)
  ...

  -- create a nested loop to check if a bullet has hit a zombie
  for _,z in ipairs(zombies) do
    for _,b in ipairs(bullets) do
      if distanceBetween(z.x, z.y, b.x, b.y) < 20 then
        -- when a bullet hits a zombie, set the dead flag to true for both the bullet and the zombie
        z.dead = true
        b.dead = true
      end
    end
  end
end

...

function distanceBetween(x1, y1, x2, y2)
  return math.sqrt((x1 - x2)^2 + (y1 - y2)^2)
end

```

Above, we only set the flag to true, but we did not actually delete them, so we must do two additional loops to delete them.

```lua
...

function love.update(dt)
  ...

  for _,z in ipairs(zombies) do
    for _,b in ipairs(bullets) do
      if distanceBetween(z.x, z.y, b.x, b.y) < 20 then
        z.dead = true
        b.dead = true
      end
    end
  end

  -- create a reverse loop to delete the dead zombies
  for i=#zombies, 1, -1 do
    if zombies[i].dead then
      table.remove(zombies, i)
    end
  end

  -- create a reverse loop to delete the dead bullets
  for i=#bullets, 1, -1 do
    if bullets[i].dead then
      table.remove(bullets, i)
    end
  end
end

...

```
The key point here is why we don't directly delete zombies and bullets in the nested loop, but instead delete them in two reverse loops? This is because deleting in the nested loop would cause index errors, so we must delete in the reverse loop to avoid affecting the index issue. This is also why we need to establish a dead flag.


