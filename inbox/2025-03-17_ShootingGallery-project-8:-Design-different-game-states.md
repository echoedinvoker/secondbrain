---
date: 2025-03-17
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# ShootingGallery project 8: Design different game states

Usually games will have different states, rather than starting the game immediately. These states can be settings before the game starts, pauses during the game, post-game calculations, and so on. In this project, we will simply use two states: 1. Game stopped 2. Game in progress.

```lua
function love.load()
  ...
  score = 0
  timer = 0
  --      ^ change the initial value of the timer from 10 to 0 because we'll reset the timer to 10 when the game starts at the bottom codes

  -- Previously, we simply used isGameOver to indicate whether the game has ended in order to avoid the timer becoming negative.
  -- But now that we have added game state, we can directly use game state instead of isGameOver.
  -- isGameOver = false
  gameState = 1 -- 1 = stopped, 2 = playing
  ...
end

function love.update(dt)
  -- if isGameOver then return end
  if gameState == 1 then return end  -- replace isGameOver with gameState
  local newTimer = timer - dt
  if newTimer > 0 then
    timer = newTimer
  else
    timer = 0
    -- isGameOver = true
    gameState = 1 -- replace isGameOver with gameState
  end
end

function love.draw()
  ...

  if gameState == 2 then -- only draw the target when the game is in progress
    love.graphics.draw(sprites.target, target.x - target.radius, target.y - target.radius)
  end
  love.graphics.draw(sprites.crosshairs, love.mouse.getX() - 20, love.mouse.getY() - 20)
end

function love.mousepressed(x, y, button, istouch, presses)
  -- add this section on top of original code to start the game when the player clicks the left mouse button while the game is stopped
  if gameState == 1 and button == 1 then
    -- reset the score and timer
    score = 0
    timer = 10
    -- change the game state to playing
    gameState = 2
    return -- return to avoid the original code from running
  end

  -- original code
  local distance = mouseToTarget(x, target.x, y, target.y)
  if button == 1 and distance < target.radius then
    score = score + 1
    target.x = math.random(target.radius, love.graphics.getWidth() - target.radius)
    target.y = math.random(target.radius, love.graphics.getHeight() - target.radius)
  end
end

...

```

As above, there are many details that need to be adjusted based on the different states of the game. Which objects should be displayed, which should be hidden, which actions should be executed, and which should not be executed all need to be handled differently based on the game state. Additionally, when switching game states, consideration should also be given to which values need to be reset and which do not need to be reset. These are all important things to pay attention to.


