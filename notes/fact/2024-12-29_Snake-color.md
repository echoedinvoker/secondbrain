---
date: 2024-12-29
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Snake color

In [[2024-12-28_Render-snake-cells|this topic]], we already rendered all cells of the snake in JS. Now, let's color cells differently between the head and the body. 


```ts
// www/index.ts

import init, { World, Direction } from "snake_game";

init().then(wasm => {
  const FPS = 10;
  const CELL_SIZE = 10;
  const WORLD_WIDTH = 8;
  const spawn_snake_head_idx = Date.now() % (WORLD_WIDTH * WORLD_WIDTH);
  const world = World.new(WORLD_WIDTH, spawn_snake_head_idx, 3);

  const snakeCellPtr = world.snake_cells();
  const snakeLength = world.snake_cells_len();

  const worldSize = world.width();

  const canvas = <HTMLCanvasElement>document.getElementById("snake-canvas");
  canvas.width = worldSize * CELL_SIZE;
  canvas.height = worldSize * CELL_SIZE;
  const ctx = canvas.getContext("2d");

  document.addEventListener("keydown", e => { ... })


  function drawWorld() { ... }

  function drawSnake() {
    const snakeCells = new Uint32Array(
      wasm.memory.buffer,
      snakeCellPtr,
      snakeLength
    )

    snakeCells.forEach((cellIdx, index) => {
      const col = cellIdx % worldSize;
      const row = Math.floor(cellIdx / worldSize);

      // add this line to color the head with a different color than the body
      ctx.fillStyle = index === 0 ? "#7878db" : "#000000";

      ctx.beginPath();
      ctx.fillRect(
        col * CELL_SIZE,
        row * CELL_SIZE,
        CELL_SIZE,
        CELL_SIZE,
      );
    })

    ctx.stroke();
  }

  function paint() {
    drawWorld();
    drawSnake();
  }

  function update() {
    setTimeout(() => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      world.update();
      paint();
      requestAnimationFrame(update)
    }, 1000 / FPS)
  }

  paint()
  update()
})

```

![colored-head.png](../assets/imgs/colored-head.png)

