---
date: 2025-01-06
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Check if body contains reward

In [[2025-01-06_Random-function|this topic]], we change the way to generate random number but it still has a tiny chance that the reward cell is generated inside the snake body. We need to check if the reward cell is inside the snake body and regenerate the reward cell if it is.

```rs
// src/lib.rs

// ...

#[wasm_bindgen]
#[derive(Clone, PartialEq)]
//              ^^^^^^^^^ this is need to compare SnakeCell by .contains() method
pub struct SnakeCell(usize);

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
    pub fn new(width: usize, spwan_idx: usize, size: usize) -> Self {
        // let reward_cell = rnd(width * width);  // remove this line because we'll generate the cell inside the loop
        let mut reward_cell;  // declare reward_cell here with mut keyword because it will be changed if it is inside the snake body
        let body: Vec<SnakeCell> = (0..size).map(|x| SnakeCell(spwan_idx + x)).collect(); // extract from bellow codes because we need to use it to check if the reward cell is inside the snake body

        // use `loop` to run a infinite loop until we find the cell that is not inside the snake body
        loop {
            reward_cell = rnd(width * width); // generate random reward cell
            if !body.contains(&SnakeCell(reward_cell)) { // check if the reward cell is inside the snake body
            //      ^^^^^^^^^^^^^^^^^^^^ this codes will check if SnakeCell has `PartialEq` trait, if not, we need to implement it
                break; // if the reward cell is not inside the snake body, break the loop
            }
        }

        World {
            width,
            snake: Snake {
                // body: (0..size).map(|x| SnakeCell(spwan_idx + x)).collect(),  -- move the value to above
                body,
                direction: Direction::Up,
            },
            next_cell: None,
            reward_cell,
        }
    }
    pub fn width(&self) -> usize {
        self.width
    }
    pub fn snake_head_idx(&self) -> usize {
        self.snake.body[0].0
    }
    pub fn reward_cell(&self) -> usize {
        self.reward_cell
    }
    pub fn change_direction(&mut self, direction: Direction) {
        let next_cell = self.gen_next_snake_head(direction);
        if self.snake.body[1].0 == next_cell.0 {
            return;
        }
        self.next_cell = Some(next_cell);
        self.snake.direction = direction;
    }
    pub fn snake_cells(&self) -> *const SnakeCell {
        self.snake.body.as_ptr()
    }
    pub fn snake_cells_len(&self) -> usize {
        self.snake.body.len()
    }

    pub fn gen_next_snake_head(&self, direction: Direction) -> SnakeCell {
        let snake_idx = self.snake_head_idx();

        let row = snake_idx / self.width;

        let world_size = self.width * self.width;

        match direction {
            Direction::Up => {
                if snake_idx < self.width {
                    SnakeCell(world_size - (self.width - snake_idx))
                } else {
                    SnakeCell(snake_idx - self.width)
                }
            }
            Direction::Down => {
                if (snake_idx + self.width) >= world_size {
                    SnakeCell(snake_idx + self.width - world_size)
                } else {
                    SnakeCell(snake_idx + self.width)
                }
            }
            Direction::Left => {
                if (snake_idx - 1) < row * self.width {
                    SnakeCell(row * self.width + self.width - 1)
                } else {
                    SnakeCell(snake_idx - 1)
                }
            }
            Direction::Right => {
                if (snake_idx + 1) >= (row + 1) * self.width {
                    SnakeCell(row * self.width)
                } else {
                    SnakeCell(snake_idx + 1)
                }
            }
        }
    }
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
    }
}


```
