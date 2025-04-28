---
date: 2025-03-16
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# ShootingGallery project 4: add Score when mouse left clicking but only inside the target

In [[2025-03-16_ShootingGallery-project:-add-Score-when-mouse-left-clicking|ShootingGallery project: add Score when mouse left clicking]], we added a score when the mouse left-clicked. However, the score was added even when the mouse was clicked outside the target. In this article, we will add a condition to add the score only when the mouse is clicked inside the target.

In order to achieve this goal, we need to determine if the position of the mouse click is within the range of the target. Since the target is a circle, we can determine if it is within range by calculating the distance between the mouse and the target.

```lua
function love.load() ... end

function love.draw() ... end

function love.mousepressed(x, y, button, istouch, presses) ... end

-- create a function to calculate the distance between two points
function mouseToTarget(x1, x2, y1, y2)
  return math.sqrt((x1 - x2)^2 + (y1 - y2)^2)
end
```

Then, we can compare the distance between the mouse click and the target's center to the target's radius as an aditional condition to add the score.

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
  if button == 1 then
    -- besides checking if the mouse is left-clicked, we also check if the distance between the mouse and the target is less than the target's radius
    if mouseToTarget(x, target.x, y, target.y) < target.radius then
      score = score + 1 -- only add score if these two conditions are met
    end
  end
end

function mouseToTarget(x1, x2, y1, y2) ... end
```

Nested if conditions have appeared above, and the content in the condition has also become more complex. We can further refactor as follows to increase code readability.

```lua
function love.load() ... end

function love.draw() ... end

function love.mousepressed(x, y, button, istouch, presses)
  local distance = mouseToTarget(x, target.x, y, target.y) -- extract the distance calculation to a variable to make condition more readable
--^^^^^ because we only use this variable once, it should be local to the function instead of global
  if button == 1 and distance < target.radius then -- combine the two conditions into one to avoid nested if
    score = score + 1
  end
end

function mouseToTarget(x1, x2, y1, y2) ... end
```


