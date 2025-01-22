---
date: 2025-01-09
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Reward cell generate fix

In [[2025-01-09_Fn-to-gen-reward|this topic]], we implemented the logic to re-generate the reward cell when snake head eats it. But we also introduced a bug that is infinite loop when the world is totally filled with snake cells like below:

![fill-up-world.png](../assets/imgs/fill-up-world.png)
We should avoid this situation from happening, because it will cause the entire browser to crash.

```rs
// src/lib.rs

// ...

#[wasm_bindgen]
impl World {

    // ...

    pub fn snake_cells_len(&self) -> usize {
        self.snake.body.len()
    }

    pub fn gen_next_snake_head(&self, direction: Direction) -> SnakeCell { ... }

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

        if self.reward_cell == self.snake_head_idx() {
            self.snake.body.push(SnakeCell(self.snake.body[1].0));

            // self.reward_cell = Self::gen_reward_cell(self.width, &self.snake.body);  // instead of re-generate reward_cell directly, we should check if the world is filled with snake cells

            // check if the world is filled with snake cells
            if self.snake_cells_len() < self.width * self.width {
                self.reward_cell = Self::gen_reward_cell(self.width, &self.snake.body); // re-generate reward_cell when still have empty
            } else {
                self.reward_cell = 10000;  // further more, if the world is filled with snake cells, we set reward_cell to the position that is out of the world
                                           // it will prevent the infinite because the sanke_head will never eat the reward_cell again
                                           // and in JS, we can know that the game is over when the reward_cell is out of the world
            }
        }
    }
}
```

By checking the position of the reward cell, we can know if the game is over or not. 

```ts
// www/index.ts

import init, { World, Direction } from "snake_game";
import { rnd } from "./utils/rnd";

init().then(wasm => {

  // ...

  function drawRewardCell() {
    const rewardCellIdx = world.reward_cell();

    // check if the reward cell is out of the world, it means the game is over
    if (rewardCellIdx === 10000) {
      alert("You win!")
    }

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
