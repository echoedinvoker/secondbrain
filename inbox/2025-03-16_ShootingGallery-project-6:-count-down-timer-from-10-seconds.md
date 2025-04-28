---
date: 2025-03-16
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# ShootingGallery project 6: count down timer from 10 seconds

Simply clicking on the target to increase points is not enough to make the game competitive. To add a sense of urgency, we will add a countdown timer that starts at 10 seconds and counts down to zero. When the timer reaches zero, the game will end, and the player's score will be displayed.

```lua
function love.load()
  target = {}
  target.x = 300
  target.y = 300
  target.radius = 50

  score = 0
  timer = 10 -- let timer start at 10 seconds

  gameFont = love.graphics.newFont(40)
end

-- refresh the timer in the functin love.update, it'll be called every frame
function love.update(dt)
--                   ^^ delta time, which will be the time between previous frame and current frame
--                      so when 1 second has passed, sum of dt will always be 1 no matter how many frames have passed, and that's what we want exactly
  timer = timer - dt -- refresh the timer by subtracting dt from it
end

function love.draw()
  love.graphics.setColor(1, 0, 0)
  love.graphics.circle("fill", target.x, target.y, target.radius)

  love.graphics.setColor(1, 1, 1)
  love.graphics.setFont(gameFont)
  love.graphics.print("Score: " .. score, 0, 0)
  love.graphics.print("Timer: " .. timer, 200, 0) -- display the timer on the screen
end

function love.mousepressed(x, y, button, istouch, presses) ... end
...

```

Now we can see a timer appearing on the screen, it will start counting down from 10, but there are still two problems:
1. The timer has too many decimal places.
2. The timer does not stop at 0. It will continue to count down into negative numbers.

Let's address the first issue by rounding the timer to the next integer by using the `math.ceil()` or `math.floor()` function. We will use `math.ceil()` to round up the timer to the next integer.

```lua
function love.load() ... end

function love.update(dt)
  timer = timer - dt
end

function love.draw()
  ...
  love.graphics.print("Timer: " .. math.ceil(timer), 200, 0)
  --                               ^^^^^^^^^ wrap the timer in math.ceil() to round it up to the next integer
  --                                         we didn't mutate the timer itself, we just displayed it as a rounded number (not changing the raw value is best practice)
end
...

```

Now the timer will display only integer values, but it will still count down into negative numbers. To prevent this, we will add a condition to stop the timer at 0.

```lua
function love.load() ... end

function love.update(dt)
  if timer > 0 then -- only update the timer if it's greater than 0
    timer = timer - dt
  end
end

function love.draw() ... end
...

```
However, during the last refresh, dt may be greater than timer, causing timer to become negative. Therefore, we need to set timer to 0 when it becomes negative.
```lua
...
function love.update(dt)
  if timer > 0 then
    timer = timer - dt
  elseif timer < 0 then
    timer = 0
  end
end
...

```

Although we corrected the situation of the timer becoming negative by resetting it to 0, this writing still results in the timer displaying a negative number for 1 frame before returning to 0. We should completely avoid the timer displaying negative numbers.
```lua
...
function love.update(dt)
  local newTimer = timer - dt
  if newTimer > 0 then
    timer = newTimer
  else
    timer = 0
  end
end
...

```

Above, we completely avoided the situation of the timer displaying negative numbers, but each frame in love.update will still unnecessarily calculate the variable `newTimer`. We can directly add a global variable to determine if the game has already ended, to avoid unnecessary calculations of the variable `newTimer` after the game has ended.

```lua
function love.load()
  --...
  isGameOver = false -- add a global variable to determine if the game has ended
  --...
end

function love.update(dt)
  if isGameOver then return end -- if the game has ended, use `return` to exit the function immediately
  local newTimer = timer - dt
  if newTimer > 0 then
    timer = newTimer
  else
    timer = 0
    isGameOver = true -- set isGameOver to true when the newTimer is less than or equal to 0 (which means the game has ended)
  end
end
```

