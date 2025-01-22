---
date: 2024-12-27
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Snake directions

We want to let the snake can move in four directions: up, down, left, and right this topic.

So we want to give a new field `direction` to the `Snake` struct and a new enum `Direction` to represent the four directions.

```rust
// src/lib.rs

use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

struct SnakeCell(usize);

#[allow(dead_code)] // to suppress the warning about unused variants
// Add the Direction enum to represent the four directions of the snake movement
enum Direction {
    Up,
    Down,
    Left,
    Right,
}

struct Snake {
    body: Vec<SnakeCell>,
    direction: Direction,  // Add the direction field to the Snake struct
}

#[wasm_bindgen]
pub struct World {
    width: usize,
    size: usize,
    snake: Snake,
}

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize) -> Self {
        World {
            width,
            size: width * width,
            snake: Snake {
                body: vec![SnakeCell(spwan_idx)],
                direction: Direction::Up, // Initialize the direction to any of the four directions
            },                            // You can change to each of the four directions for testing
        }
    }
    pub fn width(&self) -> usize {
        self.width
    }
    pub fn snake_head_idx(&self) -> usize {
        self.snake.body[0].0
    }
    pub fn update(&mut self) {
        let snake_idx = self.snake_head_idx();
        // use `match` to handle all variations of the four directions
        let new_idx = match self.snake.direction {
            Direction::Up => {
                if snake_idx < self.width {
                    self.size - self.width + snake_idx // for the top row, move to the bottom row
                } else {
                    snake_idx - self.width // move up
                }
            }
            Direction::Down => {
                if snake_idx + self.width >= self.size {
                    snake_idx + self.width - self.size // for the bottom row, move to the top row
                } else {
                    snake_idx + self.width // move down
                }
            }
            Direction::Left => {
                if snake_idx % self.width == 0 {
                    snake_idx + self.width - 1  // for the leftmost column, move to the rightmost column
                } else {
                    snake_idx - 1  // move left
                }
            }
            Direction::Right => {
                if (snake_idx + 1) % self.width == 0 {
                    snake_idx + 1 - self.width // for the rightmost column, move to the leftmost column
                } else {
                    snake_idx + 1  // move right
                }
            }
        };
        self.snake.body[0].0 = new_idx;
    }
}

```

```bash
wasm-pack build --target web
```

Now, the snake can move in four directions: up, down, left, and right according to the direction field of the Snake struct.
