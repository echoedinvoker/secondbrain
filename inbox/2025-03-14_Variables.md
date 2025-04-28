---
date: 2025-03-14
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Variables

Variables are pieces of stored data that can be changed.

```lua
message = "Lua is awesome!" -- defining a variable with a string value

function love.draw()
  love.graphics.print(message)
  --                  ^^^^^^^ using the variable
end
```
![lua-is-awesome.png](../assets/imgs/lua-is-awesome.png)

We can set multiple variables or even assign a variable to another variable.

```lua
message = "Lua is awesome!"
output = message -- whenever message changes, output changes too, because it's a reference to message

function love.draw()
  love.graphics.print(output)
  --                  ^^^^^^ using the variable output and it references message, so it prints "Lua is awesome!"
end
```

![lua-is-awesome.png](../assets/imgs/lua-is-awesome.png)

Besides strings, we can store numbers, booleans, tables, functions, and even `nil` in variables.
And like we said, variables can be changed, so we can change the value of a variable at any time.

```lua
message = 10  -- defining a variable and storing a number
message = message * 2  -- changing the value of the variable

function love.draw()
  love.graphics.print(message)
  --                  ^^^^^^^ using the variable
end
```

![print-mutated-number.png](../assets/imgs/print-mutated-number.png)


The * above can only be used on numbers. If used on a string, an error will occur.

```lua
message = "Hello World!" -- change the value of the variable from a number to a string
message = message * 2 -- trying to multiply a string by 2

function love.draw()
  love.graphics.print(message)
  --                  ^^^^^^^ using the variable
end
```
![occur-error-bcuz-of-string-multify.png](../assets/imgs/occur-error-bcuz-of-string-multify.png)

As developers, we will often see screens like the one above. We need to learn to read the text inside to understand the cause of the error, so that we can solve the problem more quickly.


```
