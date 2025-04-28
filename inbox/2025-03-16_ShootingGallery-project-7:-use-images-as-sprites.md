---
date: 2025-03-16
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# ShootingGallery project 7: use images as sprites

We want to use images to replace the crosshair, target, and background in the game. In order to achieve this goal, we need to prepare these images and convert them into `sprites` so that they can be used in the game.

```sh
 tree
.
├── main.lua
└── sprites # create a new directory to store the image files
    ├── crosshairs.png
    ├── sky.png
    └── target.png
```

We can use the `love.graphics.newImage` function to load the images and create sprites from them. And I prefer to store all the sprites in a global table for easy access.

```lua
function love.load()
  ...

  -- create a global table to store all the sprites
  sprites = {}
  sprites.sky = love.graphics.newImage("sprites/sky.png")
  sprites.target = love.graphics.newImage("sprites/target.png")
  sprites.crosshairs = love.graphics.newImage("sprites/crosshairs.png")
  --                   ^^^^^^^^^^^^^^^^^^^^^^ use love.graphics.newImage to load the images, and it'll return a sprite object
end

function love.update(dt) ... end

function love.draw() ... end

...

```

First, we want to replace the cursor with the crosshairs sprite. We can use the `love.graphics.draw` function to draw the sprite at the target position. And use `love.mouse.getX` and `love.mouse.getY` to get the current mouse position on the screen.

```lua
function love.load()
  ...
  sprites = {}
  sprites.sky = love.graphics.newImage("sprites/sky.png")
  sprites.target = love.graphics.newImage("sprites/target.png")
  sprites.crosshairs = love.graphics.newImage("sprites/crosshairs.png")
end

function love.update(dt) ... end

function love.draw()
  ...
  love.graphics.draw(sprites.crosshairs, love.mouse.getX(), love.mouse.getY())
end

...

```

You will find that the crosshairs will move with the mouse, but the cursor's position is at its top left corner, not at the center point, so we need to adjust its position. Assuming the width and height of the crosshairs are both 40 pixels, we can adjust its position like this.

```lua
...

function love.draw()
  ...
  love.graphics.draw(sprites.crosshairs, love.mouse.getX() - 20, love.mouse.getY() - 20)
  --                                                       ^^^^                    ^^^^ subtract half of the width and height of the crosshairs
end

...

```

In this way, the crosshairs will move with the mouse and the mouse's position will be at the center of the crosshairs. Then we can turn off the display of the mouse in `love.load`, so we won't see the original style of the mouse.

```lua
function love.load()
  ...

  sprites = {}
  sprites.sky = love.graphics.newImage("sprites/sky.png")
  sprites.target = love.graphics.newImage("sprites/target.png")
  sprites.crosshairs = love.graphics.newImage("sprites/crosshairs.png")

  love.mouse.setVisible(false)  -- set the mouse cursor invisible
end

...

```

Next, we want to replace the target with the target sprite. Same as the crosshairs, we can use the `love.graphics.draw` function to draw the target sprite at the target position.

```lua
function love.load()
  target = {}
  target.x = 300
  target.y = 300
  target.radius = 50

  score = 0
  timer = 10

  isGameOver = false

  gameFont = love.graphics.newFont(40)

  sprites = {}
  sprites.sky = love.graphics.newImage("sprites/sky.png")
  sprites.target = love.graphics.newImage("sprites/target.png")
  sprites.crosshairs = love.graphics.newImage("sprites/crosshairs.png")

  love.mouse.setVisible(false)
end

function love.update(dt) ... end

function love.draw()
  -- love.graphics.setColor(1, 0, 0)  -- because we use the sprite to represent the target, we don't need to draw the circle anymore
  -- love.graphics.circle("fill", target.x, target.y, target.radius)
  --
  -- love.graphics.setColor(1, 1, 1)  -- because there is no more setColor(1, 0, 0) above, we don't need to reset the color to white again here
  love.graphics.setFont(gameFont)
  love.graphics.print("Score: " .. score, 0, 0)
  love.graphics.print("Timer: " .. math.ceil(timer), 200, 0)

  love.graphics.draw(sprites.crosshairs, love.mouse.getX() - 20, love.mouse.getY() - 20)
  love.graphics.draw(sprites.target, target.x - target.radius, target.y - target.radius) -- draw the target sprite at the target position
  --                                          ^^^^^^^^^^^^^^^           ^^^^^^^^^^^^^^^ -- like the crosshairs, adjust the position of the target sprite to make it at the center of the target
end

...

```

The codes above will encounter an issue, which is that the target sprite will be covered by the crosshairs sprite because the crosshairs sprite is drawn later, so it will cover the target sprite. Therefore, we need to adjust the drawing order of the crosshairs sprite to make the target sprite appear above the crosshairs sprite.

```lua
...

function love.draw()
  ...

  -- just switch the drawing order of the crosshairs and target sprite
  love.graphics.draw(sprites.target, target.x - target.radius, target.y - target.radius)
  love.graphics.draw(sprites.crosshairs, love.mouse.getX() - 20, love.mouse.getY() - 20)
end

...

```

Finally, we want to replace the background with the sky sprite. Because the sky sprite should be drawn first to prevent it from being covered by any other sprites, we need to draw it at the beginning of the `love.draw` function.

```lua
...

function love.draw()
  love.graphics.draw(sprites.sky, 0, 0) -- draw the sky sprite very first

  ...
end

...

```
