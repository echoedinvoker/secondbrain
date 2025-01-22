---
date: 2025-01-04
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Change snake dir restriction

![how-to-restrict-dir.png](../assets/imgs/how-to-restrict-dir.png)


```rs
use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

#[wasm_bindgen]
#[derive(Clone)]
pub struct SnakeCell(usize);

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
        World {
            width,
            snake: Snake {
                body: (0..size).map(|x| SnakeCell(spwan_idx + x)).collect(),
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

    // 3) When we change the direction of the snake, we need to check if the new direction is opposite to the current direction.
    //    Because we already refactored the `gen_next_snake_head` method to accept a direction argument,
    //    so it's easy to check if the new direction is opposite to the current direction.
    pub fn change_direction(&mut self, direction: Direction) {
        // get next cell with the new direction
        let next_cell = self.gen_next_snake_head(&direction);
     
        // check if the index of next cell is the same as the index of the second cell in the snake body
        // if yes, then the new direction is opposite to the current direction, so we should not change the direction
        if self.snake.body[1].0 == next_cell.0 {
            return;
        }

        self.snake.direction = direction;
    }
    pub fn snake_cells(&self) -> *const SnakeCell {
        self.snake.body.as_ptr()
    }
    pub fn snake_cells_len(&self) -> usize {
        self.snake.body.len()
    }

    // 1) We already have a method to get next snake head cell but use current direction,
    //    need to add a argument to pass the direction so that we can use this method to predict the next cell with any given direction
    pub fn gen_next_snake_head(&self, direction: &Direction) -> SnakeCell {
    //                              ^^^^^^^^^^^^^^^^^^^^^^^
        let snake_idx = self.snake_head_idx();

        let row = snake_idx / self.width;

        let world_size = self.width * self.width;

        match direction {
        //    ^^^^^^^^^ before we were using self.snake.direction (current direction) to calculate the next cell
        //              now we will use the direction passed as argument
            
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
    // 2) because method `gen_next_snake_head` need a argument `direction`, 
    //    we need to fix the `step` method to pass the current direction to `gen_next_snake_head`
    pub fn step(&mut self) {
        let temp = self.snake.body.clone();
        self.snake.body[0] = self.gen_next_snake_head(&self.snake.direction);
        //                                            ^^^^^^^^^^^^^^^^^^^^^ pass current direction to make this method work as before
        (1..self.snake.body.len()).for_each(|i| self.snake.body[i].0 = temp[i - 1].0);
    }
}
```
We have already implemented the new feature, but the RUST compiler gave some error messages as follows:

```rs
use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

#[wasm_bindgen]
#[derive(Clone)]
pub struct SnakeCell(usize);

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
// ^^^^^^^^^^^^^^^
// 1. the trait bound `Direction: wasm_bindgen::convert::RefFromWasmAbi` is not satisfied
//    the following other types implement trait `wasm_bindgen::convert::RefFromWasmAbi`:
//      SnakeCell
//      World
//      [f32]
//      [f64]
//      [i16]
//      [i32]
//      [i64]
//      [i8]
//    and 8 others [E0277]
impl World {
    pub fn new(width: usize, spwan_idx: usize, size: usize) -> Self {
        World {
            width,
            snake: Snake {
                body: (0..size).map(|x| SnakeCell(spwan_idx + x)).collect(),
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
        let next_cell = self.gen_next_snake_head(&direction);
        if self.snake.body[1].0 == next_cell.0 {
            return;
        }
        self.snake.direction = direction;
    }
    pub fn snake_cells(&self) -> *const SnakeCell {
        self.snake.body.as_ptr()
    }
    pub fn snake_cells_len(&self) -> usize {
        self.snake.body.len()
    }

    pub fn gen_next_snake_head(&self, direction: &Direction) -> SnakeCell {
//                                       ^^^^^^^^^^^^^^^^^^^^^
// Diagnostics:
// 1. the trait bound `Direction: wasm_bindgen::convert::RefFromWasmAbi` is not satisfied
//    the following other types implement trait `wasm_bindgen::convert::RefFromWasmAbi`:
//      SnakeCell
//      World
//      [f32]
//      [f64]
//      [i16]
//      [i32]
//      [i64]
//      [i8]
//    and 8 others [E0277]
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
        self.snake.body[0] = self.gen_next_snake_head(&self.snake.direction);
        (1..self.snake.body.len()).for_each(|i| self.snake.body[i].0 = temp[i - 1].0);
    }
}
```

This is because `wasm_bindgen`` requires Direction to implement the trait `wasm_bindgen::convert::RefFromWasmAbi`. We need to use `#[derive(Clone, Copy)]` to add this trait to Direction.

```rs
use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

#[wasm_bindgen]
#[derive(Clone)]
pub struct SnakeCell(usize);

#[wasm_bindgen]
#[derive(Clone, Copy)] // 1) add this line to make Direction implement wasm_bindgen::convert::RefFromWasmAbi
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
        World {
            width,
            snake: Snake {
                body: (0..size).map(|x| SnakeCell(spwan_idx + x)).collect(),
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
    //                                           ^ because Direction has `Copy` trait, we can pass it by value instead of reference
    //                                             it'll pass a copy of the value, so it's cheap to pass it by value
        let next_cell = self.gen_next_snake_head(direction);
        if self.snake.body[1].0 == next_cell.0 {
            return;
        }
        self.snake.direction = direction;
    }
    pub fn snake_cells(&self) -> *const SnakeCell {
        self.snake.body.as_ptr()
    }
    pub fn snake_cells_len(&self) -> usize {
        self.snake.body.len()
    }

    pub fn gen_next_snake_head(&self, direction: Direction) -> SnakeCell {
    //                                          ^ same as above
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
        self.snake.body[0] = self.gen_next_snake_head(self.snake.direction);
        //                                           ^ same as above
        (1..self.snake.body.len()).for_each(|i| self.snake.body[i].0 = temp[i - 1].0);
    }
}

```

Sometimes, copying a value is more efficient than passing a reference, especially when the value is small and cheap to copy. In this case, Direction is an enum with 4 variants, so it's cheap to copy. Although we add `#[derive(Clone, Copy]` to match the wasm_bindgen requirement, it's also a good practice to add it to Direction because it's cheap to copy.

