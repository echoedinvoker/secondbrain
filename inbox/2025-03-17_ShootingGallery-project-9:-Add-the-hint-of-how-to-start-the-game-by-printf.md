---
date: 2025-03-17
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# ShootingGallery project 9: Add the hint of how to start the game by printf

In [[2025-03-17_ShootingGallery-project:-Design-different-game-states|ShootingGallery project: Design different game states]], we designed the different game states and the player can left-click to start the game. However, the player may not know how to start the game. We should add a hint to tell the player how to start the game.

We can use `printf` to print the hint on the center of the screen instead of `print` because `print` has no ability to align the text.

Here is the official document of `printf`: [printf](https://love2d.org/wiki/love.graphics.printf). It lists a lot of parameters but we only interested in the `limit` and `align` parameters because it can help us to align the text.

`limit` defines a width of the space to draw the text in and `align` defines the alignment of the text inside the `limit` space. So we can use `printf` to add the hint of how to start the game as below:

```lua
...
function love.draw()
  ...

  -- add the hint of how to start the game by printf
  if gameState == 1 then  -- only show the hint when the game is not started
    love.graphics.printf("Click anywhere to begin!", 0, 250, love.graphics.getWidth(), "center")
    --           ^^^^^^^                             ^^^^^^  ^^^^^^^^^^^^^^^^^^^^^^^^  ^^^^^^^^ align the text in the center of `limit` space
    --           ^^^^^^^                             ^^^^^^  ^^^^^^^^^^^^^^^^^^^^^^^^ set the width of the `limit` space to the width of the screen
    --           ^^^^^^^                             ^^^^^^ start the `limit` space from the left of the screen and 250 pixels from the top of the screen
  end

  if gameState == 2 then
    love.graphics.draw(sprites.target, target.x - target.radius, target.y - target.radius)
  end
  love.graphics.draw(sprites.crosshairs, love.mouse.getX() - 20, love.mouse.getY() - 20)
end
...

```
