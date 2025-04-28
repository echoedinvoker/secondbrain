---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 1: Physics

In [[2025-03-18_Platformer-project-0:-Instruction|Platformer project 0: Instruction]], we cloned the Windfield repo into the folder `libraries`, and now we will demonstrate how to use it.

```sh
 tree
.
├── libraries
│   └── windfield
│       ├── README.md
│       └── windfield # we want to include this folder to main.lua
│           ├── init.lua
│           └── mlib
│               ├── Changes.txt
│               ├── LICENSE.md
│               ├── mlib.lua
│               └── README.md
└── main.lua
```

```lua
function love.load()
  wf = require("libraries.windfield.windfield")  -- include the windfield library and assign it to the variable wf
  world = wf.newWorld(0, 100) -- create a new world with gravity x=0, y=100 (which means the gravity is down)
                              -- any physics object should be added to this world to be affected by the physics
end

function love.update(dt)
  world:update(dt) -- update the world with the delta time
end
```

The above are the most basic settings to make the entire physics engine work. Next, you can add physics objects to this world and let them be affected by gravity.

```lua
function love.load()
  wf = require("libraries.windfield.windfield")
  world = wf.newWorld(0, 0)
  player = world:newRectangleCollider(360, 100, 80, 80) -- create a new rectangle collider at x=360, y=100, width=80, height=80
end

function love.update(dt)
  world:update(dt)
end

function love.draw()
  world:draw()  -- because the physics object is invisible by default, but we can use `world:draw()` to draw the physics object outline for debugging
end
```

Launch the game and you will see a rectangle falling down and finally out of the screen. This is because the rectangle is affected by gravity and there is no boundary to stop it.

Next, we want to add a platform under the rectangle to stop it from falling down. The platform is a rectangle collider as well, but it should not be affected by gravity. We can set the platform's type to `static` to make it unaffected by gravity or any other forces.

```lua
function love.load()
  wf = require("libraries.windfield.windfield")
  world = wf.newWorld(0, 100)
  player = world:newRectangleCollider(360, 100, 80, 80)
  platform = world:newRectangleCollider(200, 400, 400, 40) -- create a new rectangle collider as the platform, but it is `dynamic` by default
  platform:setType("static") -- so we need to set the platform's type to `static` to make it unaffected by any forces
end

...

```

Launch the game again and you will see the rectangle falling down and stopping on the platform.
