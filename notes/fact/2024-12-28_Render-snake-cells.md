---
date: 2024-12-28
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Render snake cells

In [[2024-12-28_Snake-Cell|this topic]], we already implemented the snake body with multiple cells. 

But the drawing codes we wrote in [[2024-12-16_Draw-snake-head|this topic]] are only for drawing the snake head. Now, we will draw the whole snake body on the canvas.

```ts
// www/index.ts

// ...

init().then(wasm => {

  // ...

  const snakeCellPtr = world.snake_cells();
  const snakeLength = world.snake_cells_len();

  // ...

  function drawSnake() {
    // because the snake is moving, so the cells' indexes are dynamic
    // we need to get the latest snake cells' indexes from the memory in each frame
    const snakeCells = new Uint32Array(
      wasm.memory.buffer,
      snakeCellPtr,
      snakeLength
    )

    // loop through all snake cells and draw them on the canvas
    snakeCells.forEach(cellIdx => {

      // we used the original drawing codes for the snake head below, but put it in a loop
      const col = cellIdx % worldSize;
      const row = Math.floor(cellIdx / worldSize);

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

![draw-cells0.png](../assets/imgs/draw-cells0.png)

![draw-cells1.png](../assets/imgs/draw-cells1.png)

So, we can see the snake body is rendered on the canvas now. But only the snake head is moving, the body is still static. We will fix this in the next topic.
