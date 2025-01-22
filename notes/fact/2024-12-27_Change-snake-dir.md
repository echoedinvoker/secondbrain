---
date: 2024-12-27
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Change snake dir

In [[2024-12-27_Refactor-update-function-for-readability|this topic]] we finished the movement logic in different direction and in [[2024-12-27_Keydown-events|this topic]] we write a listener for keydown events. Now we can combine these two to change the direction of the snake.

But we still need to create a new function in Rust to change the direction of the snake. The function will take a `Direction` enum as an argument and update the snake's direction.

```rs
// src/lib.rs

use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

struct SnakeCell(usize);

#[wasm_bindgen]  // because we need to use this enum in JS to change the direction of the snake
#[allow(dead_code)]
  pub enum Direction {
//^^^ because we need to use this enum in JS to change the direction of the snake
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
    pub fn new(width: usize, spwan_idx: usize) -> Self {
        World {
            width,
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

    // new function to change the direction of the snake, it will take a Direction enum as an argument
    // which also force enum Direction to add #[wasm_bindgen] attribute
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

Then, we can call the `change_direction` function in the `index.ts` file to change the direction of the snake.

```ts
// www/index.ts

import init, { World, Direction } from "snake_game";
//                    ^^^^^^^^^ enum from Rust, we need to instantiate a concrete direction instance and pass it to the change_direction function of the World instance

init().then(_ => {
  const FPS = 10;
  const CELL_SIZE = 10;
  const WORLD_WIDTH = 8;
  const spawn_snake_head_idx = Date.now() % (WORLD_WIDTH * WORLD_WIDTH);
  const world = World.new(WORLD_WIDTH, spawn_snake_head_idx);

  const worldSize = world.width();

  const canvas = <HTMLCanvasElement>document.getElementById("snake-canvas");
  canvas.width = worldSize * CELL_SIZE;
  canvas.height = worldSize * CELL_SIZE;
  const ctx = canvas.getContext("2d");

  document.addEventListener("keydown", e => {
    switch (e.code) {
      case "ArrowUp":
        world.change_direction(Direction.Up);  // change the direction of the snake
        //                     ^^^^^^^^^^^^ Instead of Direction::Up in Rust, we write `Direction.Up` in JS
        break;
      case "ArrowRight":
        world.change_direction(Direction.Right); // change the direction of the snake
        //                     ^^^^^^^^^^^^^^^ In Rust, `Direction::Right` is to create a instance of Direction enum, but JS don't treat enum as a type
        break;
      case "ArrowDown":
        world.change_direction(Direction.Down); // change the direction of the snake
        //                     ^^^^^^^^^^^^^^ But it still works in JS to just pass a variant of the enum to the function
        break;
      case "ArrowLeft":
        world.change_direction(Direction.Left); // change the direction of the snake
        //                     ^^^^^^^^^^^^^^ Rust will handle it well
        break;
    }
  })


  function drawWorld() { ... }

  function drawSnake() { ... }

  function paint() {
    drawWorld();
    drawSnake();
  }

  function update() {
    setTimeout(() => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      world.update();
      paint();
      requestAnimationFrame(update)
    }, 1000 / FPS)
  }

  paint()
  update()
})
```

Now, we can use the arrow keys to change the direction of the snake. 
