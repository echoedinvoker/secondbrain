---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Remove elements from the table

There are two ways to remove elements from a table:

## 1. set the value to `nil`

```lua
local t = {1, 2, 3}

t[2] = nil
```

The disadvantage of this way is that it will create a gap in the index. For example, in the above example, only t[1] and t[3] have values, while index=2 does not have a value. Therefore, this method is usually used to remove all elements from the table.


## 2. use `table.remove`

```lua
local t = {1, 2, 3}

table.remove(t, 2)
```

`table.remove` will remove the element at the specified index and move the elements behind it forward.

So in the example above, after t[2] is removed, t[3] will be moved to the position of t[2].

The drawback of this approach is that when using `table.remove` in a loop, it may cause certain elements to be skipped. To avoid this, start looping from the last element.

```lua
for i = #t, 1, -1 do  -- loop from the last element
    if t[i] == 2 then
        table.remove(t, i)
    end
end
```
