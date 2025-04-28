---
date: 2025-03-15
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Load, Update, and Draw: Three main function to make our game work

In Love2D, there are three main functions that you need to know to make your game work: `love.load`, `love.update`, and `love.draw`.

```lua
function love.load()
  -- this function is called only once at the beginning of the game
  -- So it's here we're going to specify our global veriables,
  -- adjust the window size and perform any other preliminary setup
  -- before the game fully starts.
end

function love.update(dt)
  -- this function is called every frame, dt is `delta time`
  -- by default, there are 60 frames per second
  -- so this function is called 60 times per second
end

function love.draw()
  -- this function is called every frame like `love.update`
  -- but it's only responsible for drawing things on the screen
  -- so you should NOT perform any calculations or declare any important variables here
end
```

Here is a simple example:

```lua
function love.load()
  number = 0 -- declare a global variable and set it to 0 at the beginning of the game
end

function love.update(dt)
  number = number + 1 -- increase the number by 1 every frame
end

function love.draw()
  love.graphics.print(number) -- draw the number on the screen
end
```
When you run this code, you will see a number that increases by 1 every frame.


