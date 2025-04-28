---
date: 2025-03-14
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Else and Elself

```lua
message = 0
condition = -25

if condition > 0 then
  message = 1
end

if condition <= 0 then
  message = -1 
end

function love.draw()
  love.graphics.print(message)
end
```

We can write multiple if statements as above, but we can also simplify using else.

```lua
message = 0
condition = -25

-- simplified using `else`
if condition > 0 then
  message = 1
else
  message = -1 -- this block codes will be executed if the condition is not met `condition > 0`, which is `condition <= 0`
end

function love.draw()
  love.graphics.print(message)
end
```

When there are more conditions, we can use `elseif` to simplify the code.

```lua
message = 0
condition = -25

if condition > 0 then
  message = 1
elseif condition == 0 then
  message = 0 -- when met `condition > 0` and `condition == 0`, this block codes will be executed
else
  message = -1 -- when the condition is not met `condition > 0` and `condition == 0`, this block codes will be executed
end
```

elseif can be used multiple times.

