---
date: 2025-03-17
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 8: Let zombies move to the player

We need to adjust the x and y positions of each zombie so that they move towards the player.

We have already created a function in [[2025-03-17_TopDownShooter-project-7:-Let-zombies-face-on-the-player|TopDownShooter project 7: Let zombies face on the player]] to get the angle of each zombie, so we just need to use the cosine and sine functions to get movement weights for the x and y axes.

```lua
...

function love.update(dt)
  ...

  -- change the x and y positions of each zombie in order to move them towards the player
  for _,z in ipairs(zombies) do
    z.x = z.x + math.cos(zombieMouseAngle(z)) * z.speed * dt
    z.y = z.y + math.sin(zombieMouseAngle(z)) * z.speed * dt
    --          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^   ^^^^^^^^^^^^
    --          calc the weight for the axis    base speed, multiplied by delta time to remove the frame rate dependency
  end
end

...

-- with this fn, we can get the angle of each zombie easily
function zombieMouseAngle(enemy)
  return math.atan2(player.y - enemy.y, player.x - enemy.x)
end

...

```

Above, we use the function zombieMouseAngle similar to vue's computed, so we don't need to create additional properties to access the angle between the zombie and player, which can make our code more concise.


