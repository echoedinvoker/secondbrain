---
date: 2025-01-14
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Filter the overlapping cell

```ts
// www/index.ts

import init, { World, Direction } from "snake_game";
import { rnd } from "./utils/rnd";

init().then(wasm => {

  // ...

  function drawSnake() {
    const snakeCellPtr = world.snake_cells();
    const snakeLength = world.snake_cells_len();
    const snakeCells = new Uint32Array(
      wasm.memory.buffer,
      snakeCellPtr,
      snakeLength
    )

    snakeCells

      // Filter the overlapping cell
      .filter(cellIdx => !(cellIdx > 0 && cellIdx === snakeCells[0]))
      //                   ^^^^^^^^^^^ not the first cell (head)

      .forEach((cellIdx, index) => {
        const col = cellIdx % worldSize;
        const row = Math.floor(cellIdx / worldSize);

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

  // ...

})

```

So, the overlapping body will be filtered out from the snake cells. Then, the head won't be overlapped with the body anymore.


