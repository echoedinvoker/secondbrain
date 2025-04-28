---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# TopDownShooter project 12: Remove bullets

When the bullets leave the screen, we should remove them from the game, otherwise they will continue to exist and the program will keep calculating their positions, which will waste performance.

To remove the bullets, we will use the `table.remove` function. This function is different from just setting the bullet to `nil`, because it not only removes the element from the table, but also reorganizes the table so that there are no gaps in it.

```lua
...

function love.update(dt)
  ...

  -- create a new loop to check if any bullet is out of the screen
  for i=#bullets, 1, -1 do -- loop from the last element to the first because `table.remove` will reorganize the table
  --    ^^^^^^^^ when adding # before a table, it returns the number of elements in the table
    local b = bullets[i]
    if b.x < 0 or b.y < 0 or b.x > love.graphics.getWidth() or b.y > love.graphics.getHeight() then -- check if the bullet is out of the screen
      table.remove(bullets, i) -- remove the bullet from the table by `table.remove`
    end
  end
end

...

```
