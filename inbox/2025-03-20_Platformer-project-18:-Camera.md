---
date: 2025-03-20
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 18: Camera

In [[2025-03-20_Platformer-project-17:-Spawn-objects-from-Tiled|Platformer project 17: Spawn objects from Tiled]], we've included the map from Tiled into our game. But our screen always shows the same part of the map even when the player moves. We need to make the camera follow the player.

Love2D doesn't come with a built-in camera system, but there is a library called `hump` that provides a simple camera system. 

```sh
 cd libraries/
 git clone git@github.com:vrld/hump.git
 tree
.
├── anim8
├── hump # <---
│   ├── camera.lua # hump library has lots of useful modules, but we only need the camera module
│   ├── class.lua
│   ├── docs
│   ├── gamestate.lua
│   ├── hump-0.4-2.rockspec
│   ├── README.md
│   ├── signal.lua
│   ├── spec
│   ├── timer.lua
│   ├── vector-light.lua
│   └── vector.lua
├── Simple-Tiled-Implementation
└── windfield
```
```lua
function love.load()
  ...
  cameraFile = require("libraries.hump.camera")  -- Load the camera module
  cam = cameraFile() -- create a camera object by it
  ...
end

function love.update(dt)
  ...
  cam:lookAt(player:getX(), love.graphics.getHeight() / 2) -- make the camera follow the player, use cam:lookAt(x, y) to set the camera position
  --         ^^^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ we don't want the camera to move vertically, so we set the y position to the middle of the screen
  --         ^^^^^^^^^^^^^ we only want the camera to move horizontally with the player, so we set the x position to the player's x position
end

function love.draw()
  cam:attach() -- attach the camera to the screen
    -- anything drawn between cam:attach() and cam:detach() will be drawn with the reference to the camera
    gameMap:drawLayer(gameMap.layers["Tile Layer 1"])
    world:draw()
    playerDraw()
  cam:detach() -- detach the camera from the screen

  -- anything drawn outside cam:attach() and cam:detach() will be drawn without the reference to the camera
  -- so usually, we draw the UI elements here
end

...

```
