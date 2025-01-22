---
date: 2024-12-26
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Update the world

```js
// www/index.js

import init, { World } from "snake_game";

init().then(_ => {
  const CELL_SIZE = 10;
  const world = World.new(8, 10);

  const worldSize = world.width();

  const canvas = document.getElementById("snake-canvas");
  canvas.width = worldSize * CELL_SIZE;
  canvas.height = worldSize * CELL_SIZE;

  const ctx = canvas.getContext("2d");

  function drawWorld() { ... }

  function drawSnake() { ... }

  drawWorld();
  drawSnake();

  // re-draw the world every 100ms
  setInterval(() => {
    ctx.clearRect(0, 0, canvas.width, canvas.height);  // clear the canvas before re-drawing
    drawWorld();
    drawSnake();
  }, 100)
})

```

The world is now updated every 100ms, but it appears that the snake has not moved because we have not updated the position of the snake in the world. We plan to implement the logic for snake movement in Rust and call this method in JavaScript.

```rs
// src/lib.rs 

use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

struct SnakeCell(usize);

struct Snake {
    body: Vec<SnakeCell>,
}

#[wasm_bindgen]
pub struct World {
    width: usize,
    snake: Snake,
}

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize) -> Self {
        World {
            width,
            snake: Snake {
                body: vec![SnakeCell(spwan_idx)],
            },
        }
    }
    pub fn width(&self) -> usize {
        self.width
    }
    pub fn snake_head_idx(&self) -> usize {
        self.snake.body[0].0
    }
    // Add the update method
    pub fn update(&mut self) {
        //         ^^^ because we want to update the snake's position
        self.snake.body[0].0 = (self.snake_head_idx() + 1) % (self.width * self.width);
        //                                                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^ when the snake position reaches the end of the world, it should wrap around
    }
}
```

We can further simplify `(self.width * self.width)` to a field in the `World` struct.

```rs
use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

struct SnakeCell(usize);

struct Snake {
    body: Vec<SnakeCell>,
}

#[wasm_bindgen]
pub struct World {
    width: usize,
    size: usize,  // add the size field
    snake: Snake,
}

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize) -> Self {
        World {
            width,
            size: width * width,   // initialize the size field
            snake: Snake {
                body: vec![SnakeCell(spwan_idx)],
            },
        }
    }
    pub fn width(&self) -> usize {
        self.width
    }
    pub fn snake_head_idx(&self) -> usize {
        self.snake.body[0].0
    }
    pub fn update(&mut self) {
        self.snake.body[0].0 = (self.snake_head_idx() + 1) % self.size;
        //                                                   ^^^^^^^^^ use the size field to simplify the codes
    }
}

```

```bash
wasm-pack build --target web
```

![snake-go1.png](../assets/imgs/snake-go1.png)

![snake-go2.png](../assets/imgs/snake-go2.png)

