---
date: 2025-03-24
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Adapting to Screen Size

In mobile development, it is important to consider the different screen sizes of devices.

When designing a mobile game, you need to define the core gameplay window, it is simply a screen size that the game cater to.

```lua
function love.load()
  
  coreWidth = 720
  coreHeight = 960

  ...

end

...

```

Above, we defined the core window size as 720x960, this size is the core gameplay area of our game.

However, we cannot simply scale to adapt to different screen devices, because some devices have wider screens, some narrower, which can cause unused areas or cropping issues in the horizontal or vertical direction of the game screen.


```lua
function love.load()

  coreWidth = 720
  coreHeight = 960

  -- we need to calculate the scale and shift down value
  scale = 1
  osString = love.system.getOS()
  if osString == "iOS" or osString == "Android" then
    scale = love.graphics.getWidth() / coreWidth -- scale value is calculated based on the width of the screen
  else
    scale = 0.6
  end

  love.window.setMode(coreWidth * scale, coreHeight * scale) -- set the window size based on the scale value, but this is not enough
                                                             -- all the graphics need to be scaled as well, or they will be too small or too big

  ...

end

...

function love.draw()
    
  love.graphics.scale(scale) -- scale all the graphics by multiplying the scale value

  ...

end

...

```

Then, we need to shift down the core game for the case of narrower screens.

```lua
function love.load()

  coreWidth = 720
  coreHeight = 960

  scale = 1
  shiftDown = 0 -- create a global variable to store the shift down value

  osString = love.system.getOS()
  if osString == "iOS" or osString == "Android" then
    scale = love.graphics.getWidth() / coreWidth
    shiftDown = (love.graphics.getHeight() - coreHeight * scale) / 2 / scale -- calculate the shift down value
  else
    scale = 0.6
  end

  ...

end

...

```

Now, we have the shift down value, so whenever we determine the vertical position of an object, we need to add the shift down value to it.

```lua
...

function drawMenu()
  love.graphics.setColor(1, 1, 1, 1)
  love.graphics.draw(sprite.logo, coreWidth/2, coreHeight/2 + shiftDown, 0, 1, 1, sprite.logo:getWidth()/2, sprite.logo:getHeight()/2)
  --                                                        ^^^^^^^^^^^ add the shift down value to the vertical position

  love.graphics.setFont(fonts.menuFont)
  love.graphics.printf("Tap anywhere to start", 0, coreHeight/1.5 + shiftDown, coreWidth, "center")
  --                                                              ^^^^^^^^^^^ add the shift down value to the vertical position

  love.graphics.printf("High Score: " .. highScore, 0, coreHeight/1.2 + shiftDown, coreWidth, "center")
  --                                                                  ^^^^^^^^^^^ add the shift down value to the vertical position
end

...

```

The above is an example, actually adding the value of shiftDown to any position on the Y axis.

With those additions in place, this game can now adapt to different screen sizes :D
