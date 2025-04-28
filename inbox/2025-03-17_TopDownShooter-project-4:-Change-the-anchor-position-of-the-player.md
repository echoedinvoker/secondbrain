---
date: 2025-03-17
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 4: Change the anchor position of the player

We create a global variable `tmpRotation` to store the rotation value of the player, and increase this value in each frame to make the player continuously rotate.

```lua
function love.load()
  ...

  tmpRotation = 0  -- create a global variable to store the rotation value for the player
                   -- this is a temporary variable for testing the rotation effect
end

function love.update(dt)
  ...

  tmpRotation = tmpRotation + 0.01 -- increase the rotation value every frame to make the player keep rotating
end

function love.draw()
  ...
  love.graphics.draw(sprites.player, player.x, player.y, tmpRotation)
  --                                                     ^^^^^^^^^^^ pass the rotation value to the draw function
end
```

We will find that the player rotates in a strange way because the axis of rotation is not at the center of the player, but at the left shoulder position of the player. This is because the anchor point of the `love.graphics.draw` function is by default at the top left corner of the image, not the center of the image.

Check the official documentation of [love.graphics.draw](https://love2d.org/wiki/love.graphics.draw), we can find that the seventh and eighth parameters of the `love.graphics.draw` function are `ox` and `oy`, which are the offsets of the anchor point. We can set the anchor point to the center of the player by passing the half width and half height of the player image to the `ox` and `oy` parameters.

Our player image is 35x43 pixels, so we can modify the `love.graphics.draw` function like this:

```lua
...

function love.draw()
  love.graphics.draw(sprites.background, 0, 0)
  love.graphics.draw(sprites.player, player.x, player.y, tmpRotation, nil, nil, 17.5, 21.5)
  --                                                                  ^^^^^^^^  ^^^^^^^^^^ pass the half width and half height of the player image to the ox and oy parameters to offset the anchor point to the center of the player
  --                                                                  ^^^^^^^^ the fifth and sixth parameters are for scaling the image, so we pass nil to keep the original size (nil means not to pass any value)
end
```

Now the player rotates around its center, which looks more natural. And the position of the player is at the exact center of the screen, too. (before it has a little offset to the top left corner of the screen)

However, we may not always know the size of the sprite, or there may be a chance of input error when entering values directly, so we can use the following way to get the width and height of the sprite dynamically:

```lua
...
function love.draw()
  love.graphics.draw(sprites.background, 0, 0)
  love.graphics.draw(sprites.player, player.x, player.y, tmpRotation, nil, nil, sprites.player:getWidth() / 2, sprites.player:getHeight() / 2)
  --                                                                            ^^^^^^^^^^^^^^^^^^^^^^^^^      ^^^^^^^^^^^^^^^^^^^^^^^^^^
  --                                                                            get the width and height of the player image dynamically
  --                                                                            becareful that the getWidth and getHeight functions are called after `:` not `.`
end

```
