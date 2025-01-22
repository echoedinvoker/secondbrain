---
date: 2024-12-12
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Draw the world of snake game

```js
// www/index.js

import init, { World } from "snake_game";

init().then(_ => {
  const CELL_SIZE = 10; // define the size of each cell of world
  const world = World.new(8);
  const worldSize = world.width(); // get the width of the World, which is the input parameter 8 when creating the World
                                   // it means how many cells in one line (horizontal or vertical) of the World

  const canvas = document.getElementById("snake-canvas");

  canvas.width = worldSize * CELL_SIZE; // set the width of the canvas by pixels
  canvas.height = worldSize * CELL_SIZE; // set the height of the canvas by pixels

  const ctx = canvas.getContext("2d");

  // draw the world with ctx (canvas 2d context)
  function drawWorld() {
    ctx.beginPath();  // begin to pull the path

    // we need to pull the path between ctx.beginPath() and ctx.stroke() by 2 loops
    // one for horizontal lines, the other for vertical lines

    ctx.stroke();  // draw by the path
  }

  drawWorld();
})

```

Then, we start to do our first loop to draw the vertical lines.

```js
  function drawWorld() {
    ctx.beginPath();

    for (let x = 0; x < worldSize + 1; x++) {
      ctx.moveTo(x * CELL_SIZE, 0);  // in first loop, we move the pen to the start point of the line (0, 0), in second loop, we move the pen to (10, 0), ...
      ctx.lineTo(x * CELL_SIZE, worldSize * CELL_SIZE); // in first loop, we pull a line path from (0, 0) to (0, 80), in second loop, we pull a line path from (10, 0) to (10, 80), ...
    }

    ctx.stroke();
  }

  drawWorld();
})

```

![vertical-lines.png](../assets/imgs/vertical-lines.png)

Next, we use 2nd loop to draw horizontal lines and our world is ready.

```js
  function drawWorld() {
    ctx.beginPath();

    for (let x = 0; x < worldSize + 1; x++) {
      ctx.moveTo(x * CELL_SIZE, 0);
      ctx.lineTo(x * CELL_SIZE, worldSize * CELL_SIZE);
    }

    // draw horizontal lines
    for (let y = 0; y < worldSize + 1; y++) {
      // the logic is similar to the loop for vertical lines but different in the direction
      ctx.moveTo(0, y * CELL_SIZE);  // move the pen to the start point of the line (0, 0) in the first loop, (0, 10) in the second
      ctx.lineTo(worldSize * CELL_SIZE, y * CELL_SIZE); // pull a line path from (0, 0) to (80, 0) in the first loop, (0, 10) to (80, 10) in the second
    }

    ctx.stroke();
  }

  drawWorld();
})

```
![horizontal-lines.png](../assets/imgs/horizontal-lines.png)

