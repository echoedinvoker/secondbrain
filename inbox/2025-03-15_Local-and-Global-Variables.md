---
date: 2025-03-15
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Local and Global Variables

In Lua, variables can be either local or global. Local variables are only accessible in the scope they are defined in, while global variables can be accessed from anywhere in the program.

## Global Variables

In Lua, a variable is global by default. This means that if you assign a value to a new variable without using the `local` keyword, it will be a global variable.

```lua
function getHelf(i)
  var = i / 2 -- var is a global variable
end

getHelf(10)

function love.draw()
  love.graphics.print(var)
  --                  ^^^ prints 5, `var` can be accessed from anywhere
end
```

However, as our program becomes larger, we may accidentally reuse the same variable names, which can lead to errors. To avoid this situation, we can use the `local` keyword to create local variables.


## Local Variables

When you declare a variable with the `local` keyword, it becomes a local variable. Local variables are only accessible in the scope they are defined in.

```lua
function getHelf(i)
  local var = i / 2
--^^^^^ var is a local variable
end

getHelf(10)

function love.draw()
  love.graphics.print(var)
  --                  ^^^ error: `var` is not defined
end
```

In the example above, `var` is a local variable, so it is not accessible in the `love.draw()` function. We need to return the value of `var` from the `getHelf()` function to use it in the `love.draw()` function.

```lua
function getHelf(i)
  local var = i / 2
  return var -- return the value of var to replace the function when called
end

function love.draw()
  love.graphics.print(getHelf(10))
  --                  ^^^^^^^^^^^ prints 5
end
```

