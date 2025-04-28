---
date: 2025-03-19
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 9: Animation

In love2d, we can use the anim8 package to handle animations, so we need to clone this package into our project and include it.

```sh
 cd libraries/
 git clone git@github.com:kikito/anim8.git
 cd ..
 tree
.
├── libraries
│   ├── anim8
│   │   ├── anim8.lua # This is the file we need to include into main.lua
│   │   ├── CHANGELOG.md
│   │   ├── MIT-LICENSE.txt
│   │   ├── README.md
│   │   └── spec
│   │       ├── anim8
│   │       │   ├── animation_spec.lua
│   │       │   └── grid_spec.lua
│   │       └── love-mocks.lua
│   └── windfield
└── main.lua
```
```lua
function love.load()
  anim8 = require("libraries.anim8.anim8") -- Include the anim8 package
  ...
end
...

```
Then, we need the spritesheet which contains all the frames of the animation images.
```sh
 tree
.
├── libraries
├── main.lua
└── sprites # create a new directory for the spritesheet
    └── playerSheet.png # This is the spritesheet we need
```
You can see the spritesheet has three rows and fifteen columns, each row contains the frames of one animation. So there are three animations in this spritesheet, the first row is the idle animation, the second row is the jump animation, and the third row is the run animation.

We need to load the spritesheet and create the grid for the animations.

```lua
function love.load()
  anim8 = require("libraries.anim8.anim8")

  -- just like loading the image to sprite, we need to load the spritesheet
  sprites = {}
  sprites.playerSheet = love.graphics.newImage("sprites/playerSheet.png")

  ...
end
...

```
Above, we loaded the spritesheet and stored it in the sprites table just like before. But spritesheet is not a single image, it contains multiple frames of images, so we need to create a grid with anim8 to handle the animations.

Our spritesheet image is 9210x1692 pixels, and there are 15 columns and 3 rows inside it. So each grid cell is 614x564 pixels, these are the information we need to create the grid.

```lua
function love.load()
  anim8 = require("libraries.anim8.anim8")

  sprites = {}
  sprites.playerSheet = love.graphics.newImage("sprites/playerSheet.png")

  local grid = anim8.newGrid(614, 564, sprites.playerSheet:getWidth(), sprites.playerSheet:getHeight())

  ...
end
...

```

Now we have the grid, we can create the animations from it by anim8.newAnimation() function.

```lua
function love.load()
  anim8 = require("libraries.anim8.anim8")

  sprites = {}
  sprites.playerSheet = love.graphics.newImage("sprites/playerSheet.png")

  local grid = anim8.newGrid(614, 564, sprites.playerSheet:getWidth(), sprites.playerSheet:getHeight())

  animations = {} -- create a table to store the animations
  animations.idle = anim8.newAnimation(grid("1-15", 1), 0.05)  -- create the idle animation
  --                                   ^^^^^^^^^^^^^^^ select the frames of the idle animation, 1-15 columns of the first row
  animations.jump = anim8.newAnimation(grid("1-7", 2), 0.05)
  --                                                   ^^^^ seconds per frame
  animations.run = anim8.newAnimation(grid("1-15", 3), 0.05)

  ...
end

function love.update(dt)
  ...
  animations.idle:update(dt) -- update the idle animation
end

function love.draw()
  ...
  animations.idle:draw(sprites.playerSheet, 0, 0) -- draw the idle animation
  --                   ^^^^^^^^^^^^^^^^^^^  ^^^^ where to draw the animation (top-left corner)
  --                   ^^^^^^^^^^^^^^^^^^^ this animation is from which spritesheet
end
...

```

Above, we have created an animations table, which includes three animations: idle, jump, and run. We have also implemented drawing the idle animation on the screen, making sure to update the animation and draw it on the screen.

Since a player can only display one of the three animations (idle, jump, run) at a time, we can add a property `animation` to the player table, and then assign the currently needed animation to this property. Then update and draw this property, so that the player can display one animation. Then, when needed, assign a different animation to this property to switch the player's animation.

```lua
function love.load()
  anim8 = require("libraries.anim8.anim8")

  sprites = {}
  sprites.playerSheet = love.graphics.newImage("sprites/playerSheet.png")

  local grid = anim8.newGrid(614, 564, sprites.playerSheet:getWidth(), sprites.playerSheet:getHeight())

  animations = {}
  animations.idle = anim8.newAnimation(grid("1-15", 1), 0.05)
  animations.jump = anim8.newAnimation(grid("1-7", 2), 0.05)
  animations.run = anim8.newAnimation(grid("1-15", 3), 0.05)

  ...

  player = world:newRectangleCollider(360, 100, 80, 80, {collision_class = "Player"})
  player:setFixedRotation(true)
  player.animation = animations.idle -- assign the correct animation to the player, assume the player is idle at the beginning
  player.speed = 200

  ...
end

function love.update(dt)
  ...
  player.animation:update(dt) -- instead of animations.idle:update(dt), we update the player's animation
end

function love.draw()
  ...
  player.animation:draw(sprites.playerSheet, 0, 0)  -- instead of animations.idle:draw(sprites.playerSheet, 0, 0), we draw the player's animation
end

...

```

The above pattern ensures that the player will only display one of the three animations: idle, jump, or run, and can switch the player's animation at any time by changing `player.animation`.


