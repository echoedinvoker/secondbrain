---
date: 2025-01-09
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Game Status

We want to implement the game status to our snake game, there are three status in the game:
1. **Played**: The player has played the game. (function `step` only called in this status)
2. Won: The player has won the game.
3. Lost: The player has lost the game.

Let's start implementing this feature in Rust:

```rs
// src/lib.rs

// ...

#[wasm_bindgen]
pub enum GameStatus {
    Won,
    Lost,
    Played,
}

#[wasm_bindgen]
pub struct World {
    width: usize,
    snake: Snake,
    next_cell: Option<SnakeCell>,
    reward_cell: usize,

    // Add the game status
    status: Option<GameStatus>,
    //      ^^^^^^ we use Option to make it possible to have no status
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

            // Initialize the status to None, because the game is not started yet
            status: None,
        }
    }

    // ... 

    pub fn step(&mut self) { ... }
}

```

Above, we have already added `GameStatus` to the `World` structure and initialized it to `None`. Next, we want the `step` function to only execute in the `Played` state, so we need to modify the `step` function.

```rs

// ...

#[wasm_bindgen]
impl World {

    // ...

    pub fn step(&mut self) {

        if let Some(GameStatus::Played) = self.status {  // simply wrap the whole codes of this function by `if let` 
        //     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ only work inner codes when World status is Played

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
                if self.snake_cells_len() < self.width * self.width {
                    self.reward_cell = Self::gen_reward_cell(self.width, &self.snake.body);
                } else {
                    self.reward_cell = 10000;
                }
            }

        }
    }
}

```

Above, we use `if let` instead of `match` because `match` requires checking all possible values of the enum, while we only need to check the value of `GameStatus::Played`, so `if let` is more suitable for this situation.

Now, if you run the game, the snake will not move until you change the status to `GameStatus::Played`. We will implement the status change in the next topic.


