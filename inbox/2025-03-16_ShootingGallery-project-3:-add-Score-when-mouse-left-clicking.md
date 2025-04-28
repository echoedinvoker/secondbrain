---
date: 2025-03-16
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# ShootingGallery project 3: add Score when mouse left clicking

The main key to achieving this requirement is how we can obtain the mouse left-click event in the love2d project. This event is managed by the function `love.mousepressed`, and we can find the usage of this function in the [official love2d documentation](https://love2d.org/wiki/love.mousepressed).

```lua
function love.load()
  --...
  score = 0
  --...
end

function love.draw()
  --...
  love.graphics.setColor(1, 1, 1)
  love.graphics.setFont(gameFont)
  love.graphics.print("Score: " .. score, 0, 0)
end


-- copy signature of `love.mousepressed` function from the official love2d documentation including its parameters
function love.mousepressed(x, y, button, istouch, presses) 
--                         ^^^^  ^^^^^^  ^^^^^^^^^^^^^^^^ for mobile devices, we can ignore these parameters for now
--                         ^^^^  ^^^^^^ which button is pressed, 1 for left mouse button, 2 for right mouse button, 3 for middle mouse button
--                         ^^^^ x, y are the position of the mouse when the button is pressed
  -- because we only need to add score when the left mouse button is pressed, we can use if statement to check the button
  if button == 1 then
    score = score + 1 -- add 1 to the score when the left mouse button is pressed
  end
end
```
