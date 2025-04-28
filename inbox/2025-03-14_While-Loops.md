---
date: 2025-03-14
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# While Loops

Loops are a way to repeat the same block of code multiple times. `While Loops` are a type of loop.

```lua
message = 0

while message < 5 do
--    ^^^^^^^^^^^ when this condition is true, the codes between `do` and `end` will be executed
  message = message + 1
end -- end of the loop, it will go back to the `while` statement to check the condition again

function love.draw()
  love.graphics.print(message)
  --                  ^^^^^^^ in this case, message will print 5
end

```
As developers, we need to ensure that the condition of the `while` loop will eventually become `false`, otherwise it will run indefinitely, a situation known as an `infinite loop`, which will cause your program to freeze and be unable to exit.
