---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 19: Score

When a zombie is killed, we want to increase the score and display it at the bottom of the screen.

```lua
function love.load()
  ...
  score = 0  -- we want to keep track of the score, so add this line
  ...
end

function love.update(dt)
  ...
  for _,z in ipairs(zombies) do
    for _,b in ipairs(bullets) do
      if distanceBetween(z.x, z.y, b.x, b.y) < 20 then
        z.dead = true
        b.dead = true
        score = score + 1  -- when a zombie is killed, increase the score
      end
    end
  end
  ...
end

...

function love.keypressed(key)
  if gameState == 1 and key == 'space' then
    gameState = 2
    score = 0  -- when the game starts, reset the score to 0
  end
end

...

```
I have finished writing the logic for getting and resetting the score above, but it has not been displayed on the screen yet. Let's continue to complete this part.

```lua
...

function love.draw()
  love.graphics.draw(sprites.background, 0, 0)

  love.graphics.setFont(myFont)
  if gameState == 1 then
    love.graphics.printf('Press "space" to begin!', 0, 50, love.graphics.getWidth(), 'center')
  end

  -- draw the score at the bottom of the screen
  love.graphics.printf('Score: ' .. score, 0, love.graphics.getHeight() - 100, love.graphics.getWidth(), 'center')
  --                                                                    ^^^^^ this is the padding from the bottom of the screen

  ...
end

...

```

