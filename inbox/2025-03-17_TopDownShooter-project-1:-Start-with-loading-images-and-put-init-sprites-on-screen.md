---
date: 2025-03-17
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 1: Start with loading images and put init sprites on screen

Unlike [[2025-03-15_ShootingGallery-project-0:-Introduction|ShootingGallery]], in this project we load all the necessary images as sprites from the beginning, and place the sprites needed at the start of the game on the screen.

```sh
 tree
.
├── main.py
└── sprites # folder for images and downloaded all required images and put them in this folder
    ├── background.png
    ├── bullet.png
    ├── player.png
    └── zombie.png
```

```lua
function love.load()
  -- create a global table to store all the sprites
  sprites = {}
  sprites.background = love.graphics.newImage('sprites/background.png')
  sprites.player = love.graphics.newImage('sprites/player.png')
  sprites.bullet = love.graphics.newImage('sprites/bullet.png')
  sprites.zombie = love.graphics.newImage('sprites/zombie.png')
end

function love.draw()
  -- draw the background image at the top of `love.draw()` to make sure it is drawn first
  love.graphics.draw(sprites.background, 0, 0)
end
```

The player is also one of the sprites that appears at the beginning, so we need to draw it in `love.draw()`. However, the position of the player needs to be continuously tracked, so we need to create a global table to store the player's information and use this table's information in `love.graphics.draw()` to draw the player at the correct position.

```lua
function love.load()
  sprites = {}
  sprites.background = love.graphics.newImage('sprites/background.png')
  sprites.player = love.graphics.newImage('sprites/player.png')
  sprites.bullet = love.graphics.newImage('sprites/bullet.png')
  sprites.zombie = love.graphics.newImage('sprites/zombie.png')

  -- create a global table to store the player's information
  player = {}
  player.x = love.graphics.getWidth() / 2
  player.y = love.graphics.getHeight() / 2
  --         ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ set the player's initial position to the center of the screen
end

function love.draw()
  love.graphics.draw(sprites.background, 0, 0)
  love.graphics.draw(sprites.player, player.x, player.y) -- draw the player with the information stored in the `player` table
end
```

Now, the player sprite is drawn at the center of the screen. But if you look closely, the player sprite is drawn with its top-left corner at the center of the screen. To draw the player sprite with its center at the center of the screen, we need to adjust the position by half of the player sprite's width and height. We'll do it later.


