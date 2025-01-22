---
date: 2025-01-05
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Init reward cell

In this topic, we are going to start adding reward cells to the snake game. Let's first create the simplest part.
1. Add reward cell to the world and create a getter for JS to get the reward cell index.
2. draw the reward cell on the canvas in JS.

```rs
// src/lib.rs

// ...

#[wasm_bindgen]
pub struct World {
    width: usize,
    snake: Snake,
    next_cell: Option<SnakeCell>,
    reward_cell: usize,  // add reward cell index to the world (JS drawing only need the index)
}

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize, size: usize) -> Self {
        World {
            width,
            snake: Snake {
                body: (0..size).map(|x| SnakeCell(spwan_idx + x)).collect(),
                direction: Direction::Up,
            },
            next_cell: None,
            reward_cell: 60, // set reward cell index as a fixed value right now, we will make it random in the future
        }
    }

    // getter for JS
    pub fn reward_cell(&self) -> usize {
        self.reward_cell
    }

    // ...
}
```

Then, we can draw the reward cell on the canvas in JS.

```ts
// www/index.ts

import init, { World, Direction } from "snake_game";

init().then(wasm => {

  // ...

  function drawWorld() { ... }

  const snakeCellPtr = world.snake_cells();
  const snakeLength = world.snake_cells_len();

  function drawSnake() {
    const snakeCells = new Uint32Array(
      wasm.memory.buffer,
      snakeCellPtr,
      snakeLength
    )

    snakeCells.forEach((cellIdx, index) => {
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

  // create a function to draw the reward cell
  function drawRewardCell() {
    const rewardCellIdx = world.reward_cell();  // use the getter to get the reward cell index

    // drawing logic is similar to draw the snake but only draw one cell
    const col = rewardCellIdx % worldSize;
    const row = Math.floor(rewardCellIdx / worldSize);
    ctx.fillStyle = "#ff0000";
    ctx.beginPath();
    ctx.fillRect(
      col * CELL_SIZE,
      row * CELL_SIZE,
      CELL_SIZE,
      CELL_SIZE,
    );
    ctx.stroke();
  }

  function paint() {
    drawWorld();
    drawSnake();
    drawRewardCell();  // call the new function to draw the reward
  }

  function update() { ... }

  paint()
  update()
})

```
