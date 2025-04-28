---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 15: auto spawn zombies when the game is in session

In [[2025-03-17_TopDownShooter-project-6:-Spawn-Zombies|TopDownShooter project 6: Spawn Zombies]], we created a script that spawns zombies by pressing `space` but it just for testing purposes. Now, we want to spawn zombies automatically when the game is in session.

```lua
function love.load()
  ...

  gameState = 2 -- 1: game over, 2: playing
  maxTime = 2 -- seconds between each zombie spawn
  timer = maxTime -- we'll count down from maxTime to 0, when it reaches 0, we'll spawn a zombie
end
```
As mentioned above, we'll use `timer` to count down from `maxTime` to 0. When it reaches 0, we'll spawn a zombie.

```lua
...

function love.update(dt)
  ...

  -- when the game is in session
  if gameState == 2 then
    timer = timer - dt -- count down the timer
    if timer <= 0 then -- when the timer reaches 0
      spawnZombie() -- spawn a zombie
      maxTime = 0.95 * maxTime -- decrease the time between each zombie spawn to make the game harder
      timer = maxTime -- reset the timer for the next zombie spawn
    end
  end
end

...

```

Now, zombies will spawn automatically when the game is in session but even the game is over, zombies will still spawn. That's because we haven't refreshed the `gameState` when the game is over.

```lua
function love.update(dt)
  ...

  -- we've written the code to detect the collision between the player and zombies before
  -- if the player collides with a zombie, the game is over, and all the zombies will be removed
  for _,z in ipairs(zombies) do
    z.x = z.x + math.cos(zombieMouseAngle(z)) * z.speed * dt
    z.y = z.y + math.sin(zombieMouseAngle(z)) * z.speed * dt

    if distanceToPlayer(z.x, z.y) < 30 then -- if the player collides with a zombie
      for i,_ in ipairs(zombies) do
        zombies[i] = nil
        gameState = 1  -- simply add this line to refresh the gameState, then the zombies will stop spawning
      end
    end
  end

  ...

  if gameState == 2 then
    timer = timer - dt
    if timer <= 0 then
      spawnZombie()
      maxTime = 0.95 * maxTime
      timer = maxTime
    end
  end
end

...

```
