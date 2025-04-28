---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 17: set random seed

You may find that every time you restart the game, the first zombie appears in the same position, even though we use `math.random()`. This is because `math.random()` generates random numbers based on a seed. If the seeds are always the same, the random numbers generated will also be the same. Therefore, we need to give a different seed each time we start the game to ensure it is different from the previous game.

```lua
function love.load()
  math.randomseed(os.time()) -- use `math.randomseed` to set a random seed
  --              ^^^^^^^^^ this is current time from the operating system of the computer
  ...
end

...

```
