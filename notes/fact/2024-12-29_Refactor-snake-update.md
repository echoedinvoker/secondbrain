---
date: 2024-12-29
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Refactor snake update

In the [[2024-12-27_Refactor-update-function-for-readability|previous topic]], the update function we wrote only changes the index of the snake head. This logic is not sufficient for updating the entire snake body.

We need to change the logic of the update to generate a new `SnakeCell`, so that we can easily implement the function of updating the entire snake body.


```rs
// src/lib.rs

use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

#[wasm_bindgen]
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
    pub fn change_direction(&mut self, direction: Direction) {
        self.snake.direction = direction;
    }
    // **forbidden the old update function**
    // 
    // fn index_to_row_col(&self, idx: usize) -> (usize, usize) {
    //     (idx / self.width, idx % self.width)
    // }
    // fn row_col_to_index(&self, row: usize, col: usize) -> usize {
    //     row * self.width + col
    // }
    // pub fn update(&mut self) {
    //     let snake_idx = self.snake_head_idx();
    //     let (mut row, mut col) = self.index_to_row_col(snake_idx);
    //     match self.snake.direction {
    //         Direction::Up => row = (row + self.width - 1) % self.width,
    //         Direction::Down => row = (row + 1) % self.width,
    //         Direction::Left => col = (col + self.width - 1) % self.width,
    //         Direction::Right => col = (col + 1) % self.width,
    //     }
    //
    //     let new_idx = self.row_col_to_index(row, col);
    //     self.snake.body[0].0 = new_idx;
    // }
    pub fn snake_cells(&self) -> *const SnakeCell {
        self.snake.body.as_ptr()
    }
    pub fn snake_cells_len(&self) -> usize {
        self.snake.body.len()
    }
    // new update logic is to generate new SnakeCell for the snake head
    pub fn gen_next_snake_head(&self) -> SnakeCell {
    //                                   ^^^^^^^^^ generate new SnakeCell for head
        let snake_idx = self.snake_head_idx();
        let row = snake_idx / self.width;
        let world_size = self.width * self.width;

        match self.snake.direction {
            Direction::Up => SnakeCell((snake_idx - self.width) % world_size),
            Direction::Down => SnakeCell((snake_idx + self.width) % world_size),
            Direction::Left => SnakeCell((row * self.width) + (snake_idx - 1) % self.width),
            Direction::Right => SnakeCell((row * self.width) + (snake_idx + 1) % self.width),
        }
    }
    // then, we can repeatedly insert the new head and remove the last element of the snake body
    // to let the whole snake body move!
    pub fn step(&mut self) {
        let new_head = self.gen_next_snake_head();
        self.snake.body.insert(0, new_head); // insert the new head to the first position of body
                                             // this way, all other cells will be moved to the next position of the body
        self.snake.body.pop(); // remove the last element of the body, or the body size will increase infinitely
    }
}
```

Because we are now using the method `step` to update the position of the snake, we need to update our `index.ts` to call this method.

Another important point is that we use `insert` to insert the new head into the body, this method actually allocates new space in the wasm memory for the entire snake body, so we need to reacquire the pointer and the length of the body.

```ts
~/D/g/s/w/snake_game > cat www/index.ts
import init, { World, Direction } from "snake_game";

init().then(wasm => {
  const FPS = 10;
  const CELL_SIZE = 10;
  const WORLD_WIDTH = 8;
  const spawn_snake_head_idx = Date.now() % (WORLD_WIDTH * WORLD_WIDTH);
  const world = World.new(WORLD_WIDTH, spawn_snake_head_idx, 3);

  // **we need to reacquire the pointer and the length of the snake body every time we update the snake body**
  // **so move these two lines into the update function**
  // const snakeCellPtr = world.snake_cells();
  // const snakeLength = world.snake_cells_len();

  const worldSize = world.width();

  const canvas = <HTMLCanvasElement>document.getElementById("snake-canvas");
  canvas.width = worldSize * CELL_SIZE;
  canvas.height = worldSize * CELL_SIZE;
  const ctx = canvas.getContext("2d");

  document.addEventListener("keydown", e => { ... })


  function drawWorld() { ... }

  function drawSnake() {
    // reacquire the pointer and the length of the snake body every time we update the snake body
    const snakeCellPtr = world.snake_cells();
    const snakeLength = world.snake_cells_len();

    const snakeCells = new Uint32Array(
      wasm.memory.buffer,
      snakeCellPtr,
      snakeLength
    )

    snakeCells.forEach((cellIdx, index) => {
      const col = cellIdx % worldSize;
      const row = Math.floor(cellIdx / worldSize);

      ctx.fillStyle = index === 0 ? "#7878db" : "#000000";

      ctx.beginPath();
      ctx.fillRect(
        col * CELL_SIZE,
        row * CELL_SIZE,
        CELL_SIZE,
        CELL_SIZE,
      );
    })

    ctx.stroke();
  }

  function paint() {
    drawWorld();
    drawSnake();
  }

  function update() {
    setTimeout(() => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      world.step();
      //   ^^^^^^^^ replace the old update function with the new step function
      paint();
      requestAnimationFrame(update)
    }, 1000 / FPS)
  }

  paint()
  update()
})

```

![complete-snake-move.png](../assets/imgs/complete-snake-move.png)
