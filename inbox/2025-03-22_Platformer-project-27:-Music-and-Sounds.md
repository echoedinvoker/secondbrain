---
date: 2025-03-22
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 27: Music and Sounds

We want to add music and sound effects to our game this time.

We can find some free music and sound effects on the website [OpenGameArt](https://opengameart.org/).

```sh
 tree
.
├── audio # create a new folder to store the music and sound effects
│   ├── jump.wav # sound effect when the player jumps
│   └── music.mp3 # background music
├── enemy.lua
├── libraries
├── main.lua
├── maps
├── player.lua
└── sprites
```

Then, we need to load the music and sound effects in the `main.lua` file.

```lua
function love.load()
  ...

  sounds = {} -- create a table to store all the sounds and music
  sounds.jump = love.audio.newSource("audio/jump.wav", "static") -- load the jump sound effect and store it in the table
  sounds.music = love.audio.newSource("audio/music.mp3", "stream") -- load the background music and store it in the table
  --                                                      ^^^^^^^^ there are two types of audio sources: "static" and "stream"
  --                                                               "static" is used for short sound effects
  --                                                               "stream" is used for longer audio files like music
  
  ...

end
...

```

Now, we can play the music and sound effects in the game. But the point is when to play them. The background music is easy one, we can play it at the beginning of the game, so we can just play it in the `love.load()` function.

```lua
function love.load()
  ...

  sounds = {}
  sounds.jump = love.audio.newSource("audio/jump.wav", "static")
  sounds.music = love.audio.newSource("audio/music.mp3", "stream")

  sounds.music:play()  -- after loading the music, play it

  ...

end
...

```

Launching the game, we can hear the background music playing. However, you will find that the music eventually stops and seems to be too loud. We can find various properties in the official documentation to adjust the volume, speed, and other settings of the music. [love.source](https://love2d.org/wiki/Source)

```lua
function love.load()
  love.window.setMode(1000, 768)
  sti = require("libraries.Simple-Tiled-Implementation.sti")
  anim8 = require("libraries.anim8.anim8")
  cameraFile = require("libraries.hump.camera")
  cam = cameraFile()

  sounds = {}
  sounds.jump = love.audio.newSource("audio/jump.wav", "static")
  sounds.music = love.audio.newSource("audio/music.mp3", "stream")
  sounds.music:setLooping(true) -- set the music to loop when it ends
  sounds.music:setVolume(0.2) -- set the volume of the music to 20% of the original volume

  sounds.music:play()

  ...

end

...

```

After music, we can play the sound effect `jump.wav` when the player jumps. It happens when the player presses the space key. So just look for the `love.keypressed()` function and add the sound effect there.

```lua
...

function love.keypressed(key)
  if key == "space" and player and player.body then
    if player.isGrounded then
      player:applyLinearImpulse(0, -4000)
      sounds.jump:play()  -- play the jump sound effect when the player jumps
    end
  end
  if key == "r" then
    loadMap('level2')
  end
end

...

```
