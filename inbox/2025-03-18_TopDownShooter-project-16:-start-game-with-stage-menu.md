---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 16: start game with stage menu

Our current `gameState` has only two states: `1: game over` and `2: playing`. We also let 1 represent the state of the game menu, so that the game starts in the menu state and waits for the player to be ready before starting the game.

```lua
function love.load()
  ...
  gameState = 1 -- 1: menu, 2: playing, start with menu state
  --          ^
  ...
end
```

In menu mode, the player cannot move or shoot bullets, but can press the `space` key to start the game.

```lua
...

function love.update(dt)
  if gameState == 2 then -- only when playing, player can move
    if love.keyboard.isDown('s') then
      player.y = player.y + player.speed * dt
    end
    if love.keyboard.isDown('w') then
      player.y = player.y - player.speed * dt
    end
    if love.keyboard.isDown('a') then
      player.x = player.x - player.speed * dt
    end
    if love.keyboard.isDown('d') then
      player.x = player.x + player.speed * dt
    end
  end
  ...
end

...

-- player can press space to start the game in menu state
function love.keypressed(key)
  if gameState == 1 and key == 'space' then
    gameState = 2
  end
end

function love.mousepressed(_, _, button)
  if button == 1 and gameState == 2 then
  --             ^^^^^^^^^^^^^^^^^^ only when playing, player can shoot
    spawnBullet()
  end
end

...

```

Now, the game starts with a menu state, and the player cannot move or shoot bullets until they press the `space` key to start the game. But there is no any visual indication about how to start the game. We need to add a hint to the screen to tell the player how to start the game.

```lua
function love.load()
  ...

  myFont = love.graphics.newFont(40)  -- new font for the hint (just to make it bigger)
end

...

function love.draw()
  love.graphics.draw(sprites.background, 0, 0)

  -- draw the hint to start the game only in menu state
  love.graphics.setFont(myFont)
  if gameState == 1 then
    love.graphics.printf('Press "space" to begin!', 0, 50, love.graphics.getWidth(), 'center')
  end

  ...
end

...

```

Lauch the game and test it. We found that when restarting the game, the player wouldn't back to the center and the zombies spawning rate is the same as the previous game ended. We need to fix these issues.

```lua
...

function love.update(dt)
  ...

  for _,z in ipairs(zombies) do
    z.x = z.x + math.cos(zombieMouseAngle(z)) * z.speed * dt
    z.y = z.y + math.sin(zombieMouseAngle(z)) * z.speed * dt

    if distanceToPlayer(z.x, z.y) < 30 then
      for i,_ in ipairs(zombies) do
        zombies[i] = nil
        gameState = 1

        -- reset the player position to the center of the screen
        player.x = love.graphics.getWidth() / 2
        player.y = love.graphics.getHeight() / 2
        -- reset the timer and maxTime to 2 seconds
        maxTime = 2
        timer = maxTime
      end
    end
  end

  ...

end

...

```
