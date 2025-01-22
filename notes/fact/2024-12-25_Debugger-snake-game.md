---
date: 2024-12-25
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Debugger snake game

We can add multiple breakpoints to the code to debug it. This is useful when we want to see the state of the application at a certain point in time. 

```js
// www/index.js

import init, { World } from "snake_game";

init().then(_ => {
  const CELL_SIZE = 10;
  const world = World.new(8, 10);

  console.log(world.snake_head_idx());

  const worldSize = world.width();

  const canvas = document.getElementById("snake-canvas");
  canvas.width = worldSize * CELL_SIZE;
  canvas.height = worldSize * CELL_SIZE;

  const ctx = canvas.getContext("2d"); // provides methods to draw on the canvas

  function drawWorld() {
    ctx.beginPath();

    for (let x = 0; x < worldSize + 1; x++) {
      debugger  // add a breakpoint here
      ctx.moveTo(x * CELL_SIZE, 0);
      ctx.lineTo(x * CELL_SIZE, worldSize * CELL_SIZE);
    }

    for (let y = 0; y < worldSize + 1; y++) {
      ctx.moveTo(0, y * CELL_SIZE);
      ctx.lineTo(worldSize * CELL_SIZE, y * CELL_SIZE);
    }

    ctx.stroke();
  }

  function drawSnake() {
    debugger  // add a breakpoint here
    const snakeIdx = world.snake_head_idx();

    const col = snakeIdx % worldSize;
    const row = Math.floor(snakeIdx / worldSize);

    ctx.beginPath();

    ctx.fillRect(
      col * CELL_SIZE,
      row * CELL_SIZE,
      CELL_SIZE,
      CELL_SIZE,
    );

    ctx.stroke();
  }

  drawWorld();
  drawSnake();
})
```

## `debugger` in the for loop

![debugger-in-for-loop.png](../assets/imgs/debugger-in-for-loop.png)
## `debugger` in the regular function

![debugger-in-regular-fn.png](../assets/imgs/debugger-in-regular-fn.png)


