---
date: 2025-03-19
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 8: Make the player only jump when grounded

In [[2025-03-19_Platformer-project-7:-Query-Colliders|Platformer project 7: Query Colliders]], we've learned how to query colliders. We can use it to check if the player is grounded before allowing them to jump.

```lua
...

function love.keypressed(key)
  if key == "space" then

    -- Check if the player is grounded by querying colliders of the player's feet
    local colliders = world:queryRectangleArea(player:getX() - 40, player:getY() + 40, 80, 2, {"Platform"})
    --                      ^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  ^^^^^  ^^^^^^^^^^^^ filter to only query "Platform" collider type
    --                      ^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  ^^^^^ query rectangle width and height, player object is 80x80
    --                      ^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ query rectangle position (top-left corner), getX() and getY() are the player's center, so we need to adjust it to the left-top of the query rectangle area
    --                      ^^^^^^^^^^^^^^^^^^ there are many ways to query colliders, this is just one of them to query colliders in a rectangle area

    if #colliders == 0 then return end -- If there are no colliders, stop the function so the player can't jump
    player:applyLinearImpulse(0, -7000)
  end
end
```

Above, we use `world:queryRectangleArea` to query a small rectangle area below the player's feet and only query colliders with the type "Platform". If it returns an empty table, it means the player is not grounded, so we stop the function and the player can't jump. Otherwise, we apply a linear impulse to the player to make them jump.
