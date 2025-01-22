---
date: 2025-01-06
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Consume reward cell

In this topic, we will implement increasing the length of the snake body after the snake head eats a reward cell.

The logic is simple, just need to check in the step function whether the snake head overlaps with the reward cell, if they overlap, then push a new SnakeCell into the snake body.

```rs
// src/lib.rs

// ...

#[wasm_bindgen]
#[derive(Clone, PartialEq)]
pub struct SnakeCell(usize);

// ...

struct Snake {
    body: Vec<SnakeCell>,
    direction: Direction,
}

#[wasm_bindgen]
pub struct World {
    width: usize,
    snake: Snake,
    next_cell: Option<SnakeCell>,
    reward_cell: usize,
}

#[wasm_bindgen]
impl World {

    // ...

    pub fn step(&mut self) {
        let temp = self.snake.body.clone();
        match &self.next_cell {
            Some(cell) => {
                self.snake.body[0] = cell.clone();
                self.next_cell = None;
            }
            None => self.snake.body[0] = self.gen_next_snake_head(self.snake.direction),
        };
        (1..self.snake.body.len()).for_each(|i| self.snake.body[i].0 = temp[i - 1].0);

        // implement the logic here
        if self.reward_cell == self.snake_head_idx() {  // check if the snake head overlaps with the reward cell
            self.snake.body.push(SnakeCell(self.snake.body[1].0));  // push a new SnakeCell into the snake body
            //                             ^^^^^^^^^^^^^^^^^^^^ type is unsize, so here is a copy, not a move
        }
    }
}
```

One thing to pay special attention to is that pushing a vector will change the location of the data in memory, so make sure to update the snake cell pointer every time the snake eats a reward cell.

```ts
~/D/g/s/w/snake_game > cat www/index.ts
import init, { World, Direction } from "snake_game";
import { rnd } from "./utils/rnd";

init().then(wasm => {

  // ...

  function drawSnake() {

    // refresh snake cells pointer and length every time we draw the snake
    const snakeCellPtr = world.snake_cells();
    const snakeLength = world.snake_cells_len();

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

  function drawRewardCell() {
    const rewardCellIdx = world.reward_cell();
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
    drawRewardCell();
  }

  function update() {
    setTimeout(() => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      world.step();
      paint();
      requestAnimationFrame(update)
    }, 1000 / FPS)
  }

  paint()
  update()
})

```

![snake-eat-reward.png](../assets/imgs/snake-eat-reward.png)


Now the snake can increase its body length by eating reward cells, but the reward cells do not disappear, so next we need to implement the disappearance of reward cells.

