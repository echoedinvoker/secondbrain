---
date: 2025-03-15
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Tables part 1: create table, insert, and access data in tables

In Lua, variables can be used to store a piece of data. Tables can be used to store multiple pieces of data. Tables usually used to store a set of related data, it makes them easier to access and organized.

```lua

testScores = {} -- when assigning something with curly braces, the target is a table, because there is nothing in the curly braces, `testScores` is an empty table.
```

There are three ways to insert data into a table:

1. Using the index of the table
```lua
testScores[1] = 95
--         ^ the index of the table, in this case, the index is 1
testScores[2] = 87
testScores[3] = 98 -- now the table `testScores` stores three test scores.

-- index can be any value, not just numbers
testScores["math"] = 95
--         ^^^^^^ this is a string, not a number, which is also a valid index
```

2. Directly assigning the value to the table in the curly braces
```lua
testScores = {95, 87, 98}

-- this way, the first value will get the index 1, the second value will get the index 2, and so on.
```

3. Using the `table.insert` function
```lua
table.insert(testScores, 95)
table.insert(testScores, 87)
table.insert(testScores, 98)
--           ^^^^^^^^^^  ^^ the value to insert
--           ^^^^^^^^^^ the table to insert the value into
```

In my opinion, the third way is the most important way to insert data into a table, because it can find the next available index for you, we don't always know how many elements are in the table, so it's hard to know what the next index should be.

We can access the data in the table by using the index of the table, like this:

```lua
message = 0
testScores = {95, 87, 98}

message = testScores[2] -- message got the value 87

function love.draw()
  love.graphics.print(message)
  --                  ^^^^^^^ print 87
end
```

Sometimes we may use a index that doesn't exist in the table:

```lua
message = 0
testScores = {95, 87, 98}

message = testScores[4] -- message got the value nil when the index doesn't exist, but this is valid in Lua, it doesn't cause an error.

function love.draw()
  love.graphics.print(message)
  --                  ^^^^^^^ error, because print() can't print nil
end
```


