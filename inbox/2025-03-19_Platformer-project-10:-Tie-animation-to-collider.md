---
date: 2025-03-19
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 10: Tie animation to collider

In [[2025-03-19_Platformer-project-9:-Animation|Platformer project 9: Animation]], we've created a simple animation for our player character but it's not tied to the player's collider yet. So we need to do that in this topic.

First, we noticed that the animation is much larger than the collider, and the animation is also not in the same position as the collider. So we need to adjust the position of the animation to match the collider and shrink it.

```lua 
...
function love.draw()
  world:draw()
  local px, py = player:getPosition() -- get the position of the player collider
  player.animation:draw(sprites.playerSheet, px, py, nil, 0.25) -- the order of the arguments are almost the same as the `love.graphics.draw` function
  --                                         ^^^^^^  ^^^  ^^^^ sx (scale x), set to 0.25 means 25% of the original size, and sy will inherit the value of sx if not provided
  --                                         ^^^^^^  ^^^ rotation, not used here so set to nil
  --                                         ^^^^^^ used the position of the player collider to fill the x and y of the animation
end
...

```
We will find that the animation appears at the bottom right corner of the player's collider, this is because the x, y of `player.animation:draw` is the top left corner of the animation, while the x, y of the collider is the center, so we need to add an offset parameter to `player.animation:draw`.

When we use grid to cut spritesheet, the player in each frame may not necessarily be at the center of the frame, so we need to manually adjust the offset parameters ox, oy of `player.animation:draw` to make the player's position correct.

```lua
...
function love.draw()
  world:draw()
  local px, py = player:getPosition()
  player.animation:draw(sprites.playerSheet, px, py, nil, 0.25, nil, 130, 300)
  --                                                            ^^^  ^^^^^^^^ offset x and y
  --                                                            ^^^ sy, not used here so set to nil
end
...

```

The positions of the animation and collider are now aligned, but the collider is a square while the animation is a rectangle, so we need to adjust the width and height of the collider to roughly match the shape of the animation.

```lua
function love.load()
  ...

  player = world:newRectangleCollider(360, 100, 40, 100, {collision_class = "Player"})
  --                                            ^^^^^^^ adjust the width and height of the collider to match the shape of the animation
  player:setFixedRotation(true)
  player.animation = animations.idle
  player.speed = 200

  ...
end

...

```

Now the animation is tied to the collider in the correct position and size. Let's try to move and jump the player to see if the animation is working correctly.

We found that we can move but cannot jump because the width and height of the player's collider have been changed, causing the position of the query collider to shift. Therefore, we need to reset the position of the query collider.

```lua
function love.load()
  ...

  player = world:newRectangleCollider(360, 100, 40, 100, {collision_class = "Player"})
  player:setFixedRotation(true)
  player.animation = animations.idle
  player.speed = 200

  ...
end

...

function love.keypressed(key)
  if key == "space" and player and player.body then
    local colliders = world:queryRectangleArea(player:getX() - 20, player:getY() + 50, 40, 2, {"Platform"})
    --                                                       ^^^^                ^^^^  ^^ to match the new collider width
    --                                                       ^^^^                ^^^^ half of the new collider height
    --                                                       ^^^^ half of the new collider width
    if #colliders == 0 then return end
    player:applyLinearImpulse(0, -7000)
  end
end

```

Let's try to jump again, and we found that the player can jump super high even out of the screen. This is because that we've mutated the collider's width and height, so the mass of the collider is not suitable anymore. We need to adjust the impulse value of the jump to make it more reasonable.

```lua
...

function love.keypressed(key)
  if key == "space" and player and player.body then
    local colliders = world:queryRectangleArea(player:getX() - 20, player:getY() + 50, 40, 2, {"Platform"})
    if #colliders == 0 then return end
    player:applyLinearImpulse(0, -4000)
    --                           ^^^^^^ adjust the impulse value to make the jump height more reasonable
  end
end
```
