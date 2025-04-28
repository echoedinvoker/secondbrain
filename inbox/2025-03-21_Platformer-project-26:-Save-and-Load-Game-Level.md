---
date: 2025-03-21
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 26: Save and Load Game Level

We want to record which level the player is at, and when the player re-enters the game, allow them to start playing from their most recent progress.

When we want to save data, we need to clarify which data we want to save in the game. We will create a new table `saveData` and save all the data that needs to be saved as properties in this table. (In this case, we will only save the `currentLevel`.)

```lua
function love.load()
  ...

  saveData = {} -- create a new table and let all the data that needs to be saved be properties in this table

  saveData.currentLevel = "level1"
--^^^^^^^^^ mutates the variable `currentLevel` as a property of the `saveData` table

  loadMap(saveData.currentLevel)
  --      ^^^^^^^^^
end

function loadMap(mapName)
  saveData.currentLevel = mapName
--^^^^^^^^^
  destroyAll()
  player:setPosition(300, 100)
  gameMap = sti("maps/" .. mapName .. ".lua")
  for _, obj in pairs(gameMap.layers["Object Layer 1"].objects) do
    spawnPlatform(obj.x, obj.y, obj.width, obj.height)
  end
  for _, obj in pairs(gameMap.layers["Object Layer 2"].objects) do
    spawnEnemy(obj.x, obj.y)
  end
  for _, obj in pairs(gameMap.layers["Flag"].objects) do
    flag.x = obj.x
    flag.y = obj.y
  end
end
```

There are two main things to clarify about saving data: when to save and when to load. Additionally, when saving data, serialization must be done. Serialization is the process of converting codes into a text-friendly format so that data can be stored in a file and correctly restored when loaded later.

We can download `show.lua` from the course link as a library to help us serialize and deserialize data.

```sh
 tree
.
├── enemy.lua
├── libraries
│   ├── anim8
│   ├── hump
│   ├── show.lua  # download from the course link
│   ├── Simple-Tiled-Implementation
│   └── windfield
├── main.lua
├── maps
├── player.lua
└── sprites
```

Then, we want to save the data to a file `data.lua` when the game just starts and the current level is set.

```lua
function love.load()
  ...

  require("libraries.show") -- include the library `show.lua`

  platforms = {}

  flag = {}
  flag.x = 0
  flag.y = 0

  saveData = {}

  saveData.currentLevel = "level1"

  loadMap(saveData.currentLevel)
end

function loadMap(mapName)
  saveData.currentLevel = mapName

  -- save the data to a file `data.lua`, if the file does not exist, it will be created right after the game is run and current level is set
  love.filesystem.write("data.lua", table.show(saveData, "saveData"))
  --                                ^^^^^^^^^^ ^^^^^^^^  ^^^^^^^^^^ a tag to identify purpose of the data
  --                                ^^^^^^^^^^ ^^^^^^^^ the data to be saved
  --                                ^^^^^^^^^^ ^^^^^^^^ the data to be saved
  --                                ^^^^^^^^^^ function from the library `show.lua` to serialize the data

  ...
end
```

When the player re-enters the game, we want to load the data from the file `data.lua` and set the current level from the loaded data.

```lua
function love.load()
  ...

  require("libraries.show")

  platforms = {}

  flag = {}
  flag.x = 0
  flag.y = 0

  saveData = {}
  saveData.currentLevel = "level1"

  -- load the data from the file `data.lua` and set the current level from the loaded data
  if love.filesystem.getInfo("data.lua") then -- check if the file exists
    local data = love.filesystem.load("data.lua") -- load the data from the file
    data() -- data is a function, it'll put the saved data into propriate variables or tables when it's called
           -- (in this case, it'll put the `saveData` table, so the value of `saveData.currentLevel` will be overwritten by it)
  end

  loadMap(saveData.currentLevel)
end

...

```

Now, when the player re-enters the game, they can start playing from their most recent progress.

Finally, you may be curious about where `data.lua` is stored, what its contents are, you can refer to the official documentation [love.filesystem](https://love2d.org/wiki/love.filesystem) to understand.

And its contents are as follows:

```lua
saveData = {
   ["currentLevel"] = "level2";
};
```


