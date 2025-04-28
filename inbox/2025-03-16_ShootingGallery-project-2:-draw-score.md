---
date: 2025-03-16
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# ShootingGallery project 2: draw score

```lua
function love.load()
  target = {}
  target.x = 300
  target.y = 300
  target.radius = 50

  score = 0 -- score is a tracking state, so it should be defined as a global variable in love.load()
  timer = 0

  -- create a font object as a global variable, and we'll use it to draw the score
  gameFont = love.graphics.newFont(40)
  --                               ^^ there are many other parameters we can pass to newFont, but we're using the default values for now
end

function love.draw()
  love.graphics.setColor(1, 0, 0) -- line 11
  love.graphics.circle("fill", target.x, target.y, target.radius)

  love.graphics.setColor(1, 1, 1) -- set the color back to white because we've changed it to red at line 11
  love.graphics.setFont(gameFont) -- set the font to the gameFont object, it'll effect all the text drawn after this line (just like setColor)
  love.graphics.print("Score: " .. score, 0, 0) -- draw the score at the top-left corner of the screen
  --                            ^^ ^^^^^  ^^^^ for x and y, we can directly use hard-coded values here because they're not going to change
  --                            ^^ ^^^^^ for dynamic values, we use variables
  --                            ^^ `..` is the concatenation operator in Lua
end
```
