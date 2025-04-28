---
date: 2025-03-17
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 3: Rotate the player

Check the official documentation for the [love.graphics.draw](https://love2d.org/wiki/love.graphics.draw) function. We can rotate the sprite by passing the radian to the parameter `r` (the fourth parameter). 

Our player sprite is facing right at 0 degrees, so if we want to rotate it downwards, we need to rotate it by 90 degrees. We must convert 90 degrees to radians so that we can pass it to the `love.graphics.draw` function.

We can convert degrees to radians by multiplying the degrees by `math.pi / 180`. So, 90 degrees is equal to `90 * math.pi / 180 = math.pi / 2`.

```lua
...
function love.draw()
  love.graphics.draw(sprites.background, 0, 0)
  love.graphics.draw(sprites.player, player.x, player.y, math.pi / 2)
  --                                                     ^^^^^^^^^^^ pass the radian to rotate the sprite
end
```

Launch the game, and you will see the player sprite rotated by 90 degrees, facing downwards.
