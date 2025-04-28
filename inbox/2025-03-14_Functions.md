---
date: 2025-03-14
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Functions

Functions are a way to group code together and reuse it to prevent code duplication.

```lua
message = 0

-- define a function that increases the message by 5
function increaseMessage():
    message = message + 5
end

function love.draw():
    love.graphics.print(message)
    --                  ^^^^^^^ still 0 because the function was never called
end
```

We should call the function to execute it:

```lua
message = 0

function increaseMessage():
    message = message + 5
end

increaseMessage() -- call the function
increaseMessage() -- we can reuse the function as many times as we want

function love.draw():
    love.graphics.print(message)
    --                  ^^^^^^^ now it's 10
end
```

We can set parameters to functions:

```lua
message = 0

function increaseMessage(amount):
--                       ^^^^^^ parameter, we can set multiple parameters
    message = message + amount
    --                  ^^^^^^ use the parameter inside the function
end

increaseMessage(10) -- call the function with the parameter 10
increaseMessage(20) -- we can reuse the function with different parameters

function love.draw():
    love.graphics.print(message)
    --                  ^^^^^^^ now it's 30
end
```

We can also return values from functions:

```lua
message = 0

function increaseMessage(amount):
    message = message + amount
end

function double(val):
    return val * 2 -- return the value, it will replace the function call
end

increaseMessage(double(10)) -- call the function double and pass the result to increaseMessage
--              ^^^^^^^^^^ double(10) will be replaced by 20

function love.draw():
    love.graphics.print(message)
    --                  ^^^^^^^ now it's 20
end
```

`increaseMessage` can only operate on `message`, while `double` is a generic function that can operate on any numerical value. This design allows us to better reuse code.

```lua
message = 0
chicken = 0

function increaseMessage(amount):  -- only works on message, not chicken
    message = message + amount
end

function double(val):  -- works on any numerical value
    return val * 2
end

message = double(12) -- double can be used on message
chicken = double(message) -- double can be used on chicken

function love.draw():
    love.graphics.print(chicken)
    --                  ^^^^^^^ now it's 48
end
```

