---
date: 2025-03-24
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Mobile touch

In a love2d game, if your game is running on mobile, touch and mouse click are the same, so you can use `love.mousepressed` to handle touch events.

However, touch is more advanced than click in mobile. For example, in mobile, you can use multi-touch or pressure-sensitive touch. If you want to use these features, you need to use `love.touch`, you can refer to the official documentation [love.touch](https://love2d.org/wiki/love.touch).

For detecting touch pressure, you can use:

```lua
love.touch.getPressure(id)
--                     ^^ each touch has an id, it is for multi-touch support
--                     ^^ and you can use `love.getTouch` to return a table of id values

```

