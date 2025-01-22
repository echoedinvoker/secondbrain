---
date: 2024-12-29
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Optimize modular operation

In [[2024-12-29_Refactor-snake-update|refactor snake update]], we finished the method of generating the next snake head and step the whole snake body.

But each direction has a modular operation, which is not efficient. We can optimize it by using other operators. (this is optional, but it's good to know)

```rs
// src/lib.rs

// ...

#[wasm_bindgen]
impl World {

    // ...
  
    pub fn gen_next_snake_head(&self) -> SnakeCell {
        let snake_idx = self.snake_head_idx();

        let row = snake_idx / self.width;  // divide operation is expensive, too. But not as expensive as modular operation
                                          // you can try to optimize it as well (if you want, I don't think it's necessary)

        let world_size = self.width * self.width;

        match self.snake.direction {
            // Direction::Up => SnakeCell((snake_idx - self.width) % world_size),
            // Direction::Down => SnakeCell((snake_idx + self.width) % world_size),
            // Direction::Left => SnakeCell((row * self.width) + (snake_idx - 1) % self.width),
            // Direction::Right => SnakeCell((row * self.width) + (snake_idx + 1) % self.width),
            //                                                                    ^ original codes have lots of modular operations,
            //                                                                      which is expensive

            // replace % with other operator for higher performance
            // my idea is to check if the next cell is out of bound,
            // if it is, set it to the other side of the world
            // if not, just return the next cell index normally
            Direction::Up => {
                if snake_idx < self.width {
                // ^^^^^^^^^^^^^^^^^^^^^^ check if next cell is out of bound
                    SnakeCell(world_size - (self.width - snake_idx))  // if so, set it to the other side of the world
                } else {
                    SnakeCell(snake_idx - self.width) // if not, return the next cell index normally
                }
            }
            // do the same logic for other directions
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
        let new_head = self.gen_next_snake_head();
        self.snake.body.insert(0, new_head);
        self.snake.body.pop();
    }
}

```
