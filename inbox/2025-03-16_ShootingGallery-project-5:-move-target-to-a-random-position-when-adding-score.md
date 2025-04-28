---
date: 2025-03-16
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# ShootingGallery project 5: move target to a random position when adding score

In [[2025-03-16_ShootingGallery-project:-add-Score-when-mouse-left-clicking-but-only-inside-the-target|ShootingGallery project: add Score when mouse left clicking but only inside the target]], we implemented the score incrementation when the player clicks inside the target. But the target stays at the same position, which is not very challenging. Let's move the target to a random position when the player adds a score.

```lua
function love.load()
  target = {}
  target.x = 300
  target.y = 300
  target.radius = 50

  score = 0
  timer = 0

  gameFont = love.graphics.newFont(40)
end

function love.draw()
  love.graphics.setColor(1, 0, 0)
  love.graphics.circle("fill", target.x, target.y, target.radius)

  love.graphics.setColor(1, 1, 1)
  love.graphics.setFont(gameFont)
  love.graphics.print("Score: " .. score, 0, 0)
end

function love.mousepressed(x, y, button, istouch, presses)
  local distance = mouseToTarget(x, target.x, y, target.y)
  if button == 1 and distance < target.radius then
    score = score + 1
    -- Move target to a random position after adding score
    target.x = math.random(0, love.graphics.getWidth())
    --         ^^^^^^^^^^^ this is built-in Lua function to generate a random number, first argument is the minimum value, second argument is the maximum value
    target.y = math.random(0, love.graphics.getHeight())
    --                        ^^^^^^^^^^^^^^^^^^^^^^^^^ we can easily get the width and height of the game window using love.graphics.getWidth() and love.graphics.getHeight()
  end
end

function mouseToTarget(x1, x2, y1, y2)
  return math.sqrt((x1 - x2)^2 + (y1 - y2)^2)
end
```

Because `target.x` and 1target.y1 represent the center of the target, although the above definition ensures that the center of the circle is within the screen, the boundary of the circle may still exceed the screen. We can further add `target.radius` to ensure that the entire circle is within the screen.

```lua
...
function love.mousepressed(x, y, button, istouch, presses)
  local distance = mouseToTarget(x, target.x, y, target.y)
  if button == 1 and distance < target.radius then
    score = score + 1
    -- vary the minimum and maximum values a bit with the radius to ensure the entire circle is within the screen
    target.x = math.random(target.radius, love.graphics.getWidth() - target.radius)
    --                     ^^^^^^^^^^^^^                           ^^^^^^^^^^^^^^^
    target.y = math.random(target.radius, love.graphics.getHeight() - target.radius)
    --                     ^^^^^^^^^^^^^                            ^^^^^^^^^^^^^^^
  end
end
...
