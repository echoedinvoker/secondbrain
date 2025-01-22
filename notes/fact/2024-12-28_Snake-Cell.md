---
date: 2024-12-28
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Snake Cell

So far, Snake only has one head cell. We need to allow multiple cells in the body to achieve a complete snake.

```rs
// src/lib.rs

use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

struct SnakeCell(usize);

#[wasm_bindgen]
#[allow(dead_code)]
pub enum Direction {
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
    snake: Snake,
}

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize, size: usize) -> Self {
    //                                         ^^^^^^^^^^^ new argument to define the size of the snake body (how many cells in the body).
        World {
            width,
            snake: Snake {
                // body: vec![SnakeCell(spwan_idx)],   // our body already been vector type, but only one cell in it.
                body: (0..size).map(|x| SnakeCell(spwan_idx + x)).collect(),
                //    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ use iteration to push multiple cells with different indexes to the body.
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

    pub fn change_direction(&mut self, direction: Direction) {
        self.snake.direction = direction;
    }

    fn index_to_row_col(&self, idx: usize) -> (usize, usize) {
        (idx / self.width, idx % self.width)
    }
    fn row_col_to_index(&self, row: usize, col: usize) -> usize {
        row * self.width + col
    }
    pub fn update(&mut self) {
        let snake_idx = self.snake_head_idx();
        let (mut row, mut col) = self.index_to_row_col(snake_idx);
        match self.snake.direction {
            Direction::Up => row = (row + self.width - 1) % self.width,
            Direction::Down => row = (row + 1) % self.width,
            Direction::Left => col = (col + self.width - 1) % self.width,
            Direction::Right => col = (col + 1) % self.width,
        }

        let new_idx = self.row_col_to_index(row, col);
        self.snake.body[0].0 = new_idx;
    }
}
```

```bash
~/D/g/s/w/snake_game > wasm-pack build --target web
```

Although multiple cells have been implemented in the Snake body in Rust, only the cell of the snake head is rendered in JS. We will implement the complete rendering of the snake body in the next topic.

