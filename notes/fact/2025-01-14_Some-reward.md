---
date: 2025-01-14
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Some reward

Previously, in our snake game, when winning the game, you could still see the reward cell in the World. We want to remove it from the World when winning.

```rs

// ...

#[wasm_bindgen]
pub struct World {
    width: usize,
    snake: Snake,
    next_cell: Option<SnakeCell>,
    reward_cell: Option<usize>,
    //           ^^^^^^^     ^ 1) we want to make reward_cell None when the game is won
    status: Option<GameStatus>,
}

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize, size: usize) -> Self {
        let body: Vec<SnakeCell> = (0..size).map(|x| SnakeCell(spwan_idx + x)).collect();

        World {
            width,
            reward_cell: Self::gen_reward_cell(width, &body),
            snake: Snake {
                body,
                direction: Direction::Up,
            },
            next_cell: None,
            status: None,
        }
    }

    fn gen_reward_cell(width: usize, body: &[SnakeCell]) -> Option<usize> {
    //                                                      ^^^^^^^     ^ 2) generated reward cell should become enum Option
        let mut reward_cell;
        loop {
            reward_cell = rnd(width * width);
            if !body.contains(&SnakeCell(reward_cell)) {
                break;
            }
        }

        Some(reward_cell)
    //  ^^^^ 2) return Some(reward_cell) instead of reward_cell
    }


    // ... 

    pub fn reward_cell(&self) -> Option<usize> {
    //                           ^^^^^^^     ^ 3) return reward_cell to JS, but now it's an Option
        self.reward_cell
    }

    // ...

    pub fn step(&mut self) {
        if let Some(GameStatus::Played) = self.status {
            let temp = self.snake.body.clone();
            match &self.next_cell {
                Some(cell) => {
                    self.snake.body[0] = cell.clone();
                    self.next_cell = None;
                }
                None => self.snake.body[0] = self.gen_next_snake_head(self.snake.direction),
            };

            (1..self.snake.body.len()).for_each(|i| self.snake.body[i].0 = temp[i - 1].0);

            if self.snake.body[1..self.snake.body.len()].contains(&self.snake.body[0]) {
                self.status = Some(GameStatus::Lost);
            }

            // if self.reward_cell == self.snake_head_idx() {
            //     self.snake.body.push(SnakeCell(self.snake.body[1].0));
            //     if self.snake_cells_len() < self.width * self.width {
            //         self.reward_cell = Self::gen_reward_cell(self.width, &self.snake.body);
            //     } else {
            //         // self.reward_cell = 10000;
            //         self.status = Some(GameStatus::Won);
            //     }
            // }
            if let Some(reward_cell) = self.reward_cell {  // 4) filter out reward_cell with `if let` pattern, and unwrap it do the above commented code logic
                if reward_cell == self.snake_head_idx() {  // or you can simple write as `if reward_cell == Some(self.snake_head_idx()) { ... }`
                    self.snake.body.push(SnakeCell(self.snake.body[1].0));  //                              ^^^^^                     ^
                    if self.snake_cells_len() < self.width * self.width {
                        self.reward_cell = Self::gen_reward_cell(self.width, &self.snake.body);
                    } else {
                        self.reward_cell = None;  // 5) set reward_cell to None when the game is won
                        self.status = Some(GameStatus::Won);
                    }
                }
            }
        }
    }
}
```

```ts
// www/index.ts

import init, { World, Direction } from "snake_game";
import { rnd } from "./utils/rnd";

init().then(wasm => {

  // ...

  function drawRewardCell() {
    const rewardCellIdx = world.reward_cell();

    // if Rust returns None, rewardCellIdx will be undefined
    if (!rewardCell) return; // to prevent drawing reward cell when the game is won

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

  // ...

})
```

