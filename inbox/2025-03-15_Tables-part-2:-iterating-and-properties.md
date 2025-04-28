---
date: 2025-03-15
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Tables part 2: iterating and properties

We can iterate over the entries of a table using the for loop.

```lua
message = 0
testScores = {95, 87, 79}

for i,s in ipairs(testScores) do
--  ^ ^    ^^^^^^^^^^^^^^^^^^ wraps the table with `ipairs` to make it iterable
--  ^ ^ value of current iteration
--  ^ index of current iteration

  message = message + s
  --                ^^^ add the value of the current iteration to the message
  --                ^^^ so it will be 0 + 95 + 87 + 79 = 261
end

function love.draw()
  love.graphics.print(message)
  --                  ^^^^^^^ print 261
end
```

Besides index and value, we can also add properties to the table, it just like to store variables in a table.

```lua
testScores = {}
testScores.subject = "Math"
--        ^^^^^^^^ add a property to the table by using the dot operator

function love.draw()
  love.graphics.print(testScores.subject)
  --                  ^^^^^^^^^^^^^^^^^^ print "Math"
end
```

It's useful to store additional information in a table.


