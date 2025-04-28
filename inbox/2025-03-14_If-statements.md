---
date: 2025-03-14
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# If statements

```lua
message = 0
condition = 25

if condition > 0 then
-- ^^^^^^^^^^^^^ we can use variables as part of the condition of an if statement
--               if `condition > 0` is true, then the code block between `then` and `end` will be executed

  message = 1 -- this line will be executed when the condition is true
              -- you can write multiple lines of code between `then` and `end`
end

function love.draw()
  love.graphics.print(message)
  --                  ^^^^^^^ in this case, the value of `message` will be 1
end

```

Beginners often forget to write code between `then` and `end` blocks, this is a common mistake, you must pay attention to which block you are in.
