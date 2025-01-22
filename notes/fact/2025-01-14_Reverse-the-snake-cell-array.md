---
date: 2025-01-14
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Reverse the snake cell array

```ts
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
      .slice()  // copy the array, or .reverse() will mutate the wasm memory also
      .reverse() // reverse the array, so below painting will start from the tail
      .forEach((cellIdx, index) => {
        const col = cellIdx % worldSize;
        const row = Math.floor(cellIdx / worldSize);

        ctx.fillStyle = index === snakeLength - 1 ? "#7878db" : "#000000";
        //                        ^^^^^^^^^^^^^^^ now the last item of array is head
        //                                        instead of the first

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

Now, the head is the last cell of the snake array, so even if it is overlapping with the body, it will be painted on top of it.


