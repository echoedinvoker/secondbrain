---
date: 2025-03-22
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 28: background image

We want to add a background image to our game this time.

```sh
 tree
.
├── audio
├── enemy.lua
├── libraries
├── main.lua
├── maps
├── player.lua
└── sprites
    ├── background.png  # download the image file and put it here
    ├── enemySheet.png
    └── playerSheet.png
```

Then, load the image in `main.lua`:

```lua
function love.load()
  ...

  sprites = {}
  sprites.playerSheet = love.graphics.newImage("sprites/playerSheet.png")
  sprites.enemySheet = love.graphics.newImage("sprites/enemySheet.png")
  sprites.background = love.graphics.newImage("sprites/background.png") -- load the background image as a sprite

  ...

end

...

```

And draw the background image in `love.draw()`, but there are two options to draw the background image: inside the camera or outside the camera.

Let's try to draw the background image inside the camera first.
```lua
...

function love.draw()
  cam:attach()
    love.graphics.draw(sprites.background, 0, 0) -- draw the background image at the top-left corner but inside the camera
    gameMap:drawLayer(gameMap.layers["Tile Layer 1"])
    world:draw()
    if player.body then
      playerDraw()
    end
    drawEnemies()
  cam:detach()
end

...

```

Launch the game and you will see the background image has only taken up the top-left corner of the screen. If you move your player to the right, you will see the background image, and that's not what we want.

So, let's draw the background image outside the camera.

```lua
...

function love.draw()
  love.graphics.draw(sprites.background, 0, 0) -- draw the background image at the top-left corner OUTSIDE the camera
  cam:attach()
    gameMap:drawLayer(gameMap.layers["Tile Layer 1"])
    world:draw()
    if player.body then
      playerDraw()
    end
    drawEnemies()
  cam:detach()
end

...

```

Launch the game again, and you will see the background image has always taken up the whole screen. That's what we want. :D

Besides background images, HUDs should be drawn outside the camera as well.

