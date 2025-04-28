---
date: 2025-03-22
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Web Game Development 2: Check if our game works on love10

In [[2025-03-22_Web-Game-Development:-Love.js-Setup-and-Installation|Web Game Development: Love.js Setup and Installation]], we said that `love.js` only supports `love2d` version v0.10.0, so we need to check if our game works on `love10`.

```sh
yay -S love10
cd /path/to/your/game
love10 .
```

The game screen of the course is black mainly because of the difference in `setColor`, so the following corrections need to be made:

```lua
-- love.graphics.setColor(1, 1, 1)
love.graphics.setColor(255, 255, 255)
```

In addition, the libraries used by the game may also have compatibility issues with love2d version 0.10.0. If this happens, you will need to switch to a different version of the libraries.

You must continue to modify until the game can run normally using 'love10'.
