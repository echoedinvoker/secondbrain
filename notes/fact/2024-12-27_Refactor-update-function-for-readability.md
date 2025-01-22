---
date: 2024-12-27
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Refactor update function for readability

In [[2024-12-27_Snake-directions|this topic]], we write the logic about how the snake moves in different directions. The logic is a bit complex and hard to understand. Let's refactor the update function to make it more readable.

```rs
~/D/g/s/w/snake_game > cat src/lib.rs
use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

struct SnakeCell(usize);

#[allow(dead_code)]
enum Direction {
    Up,
    Down,
    Left,
    Right,
}

struct Snake {
    body: Vec<SnakeCell>,
    direction: Direction,
}

#[wasm_bindgen]
pub struct World {
    width: usize,
    // size: usize,  // after refactor, we don't need to use this field in the fn `update`, so we can remove it
    snake: Snake,
}

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize) -> Self {
        World {
            width,
            // size: width * width,  // remove this line
            snake: Snake {
                body: vec![SnakeCell(spwan_idx)],
                direction: Direction::Up,
            },
        }
    }
    pub fn width(&self) -> usize {
        self.width
    }
    pub fn snake_head_idx(&self) -> usize {
        self.snake.body[0].0
    }

    // refactor idea is to get the row and col from the snake head index, then update row or col based on the direction
    // then convert row and col back to index, and update the snake head index
     
    // so we create 2 helper functions for converting index to row and col, and row and col to index
    fn index_to_row_col(&self, idx: usize) -> (usize, usize) {
        (idx / self.width, idx % self.width)
    }
    fn row_col_to_index(&self, row: usize, col: usize) -> usize {
        row * self.width + col
    }

    // simply update the col or row based on the direction, then update the snake head index
    pub fn update(&mut self) {
        let snake_idx = self.snake_head_idx();
        let (mut row, mut col) = self.index_to_row_col(snake_idx);  // get row and col from index

        // update row or col based on the direction
        match self.snake.direction {
            Direction::Up => row = (row + self.width - 1) % self.width,
            Direction::Down => row = (row + 1) % self.width,
            Direction::Left => col = (col + self.width - 1) % self.width,
            Direction::Right => col = (col + 1) % self.width,
        }

        let new_idx = self.row_col_to_index(row, col);  // convert row and col back to index
        self.snake.body[0].0 = new_idx;
    }
}
```

Now, the snake should work correctly as before, but the code is more readable and easier to understand.
