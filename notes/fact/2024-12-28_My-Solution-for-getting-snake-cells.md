---
date: 2024-12-28
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# My Solution for getting snake cells

In JS, we don't really need to get the cells. JS only needs to know each cell's position (indexes). So, I decided to add a method to get the indexes of all cells in the body.

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
        World {
            width,
            snake: Snake {
                body: (0..size).map(|x| SnakeCell(spwan_idx + x)).collect(),  // JS can't get the cells directly
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
    // add this method to get indexes of all cells in the body.
    pub fn snake_cells(&self) -> Vec<usize> {
        self.snake.body.iter().map(|cell| cell.0).collect()
    }
}
```

```ts
// www/index.ts

import init, { World, Direction } from "snake_game";

init().then(_ => {
  const FPS = 10;
  const CELL_SIZE = 10;
  const WORLD_WIDTH = 8;
  const spawn_snake_head_idx = Date.now() % (WORLD_WIDTH * WORLD_WIDTH);
  const world = World.new(WORLD_WIDTH, spawn_snake_head_idx, 3);
  //                                                         ^

  // then we can get the indexes of all cells in JS by that method.
  console.log(world.snake_cells());
  //               ^^^^^^^^^^^^^^  return array of indexes such as [31, 32, 33]

  const worldSize = world.width();

  const canvas = <HTMLCanvasElement>document.getElementById("snake-canvas");
  canvas.width = worldSize * CELL_SIZE;
  canvas.height = worldSize * CELL_SIZE;
  const ctx = canvas.getContext("2d");

  document.addEventListener("keydown", e => {
    switch (e.code) {
      case "ArrowUp":
        world.change_direction(Direction.Up);
        break;
      case "ArrowRight":
        world.change_direction(Direction.Right);
        break;
      case "ArrowDown":
        world.change_direction(Direction.Down);
        break;
      case "ArrowLeft":
        world.change_direction(Direction.Left);
        break;
    }
  })


  function drawWorld() {
    ctx.beginPath();

    for (let x = 0; x < worldSize + 1; x++) {
      ctx.moveTo(x * CELL_SIZE, 0);
      ctx.lineTo(x * CELL_SIZE, worldSize * CELL_SIZE);
    }

    for (let y = 0; y < worldSize + 1; y++) {
      ctx.moveTo(0, y * CELL_SIZE);
      ctx.lineTo(worldSize * CELL_SIZE, y * CELL_SIZE);
    }

    ctx.stroke();
  }

  function drawSnake() {
    const snakeIdx = world.snake_head_idx();

    const col = snakeIdx % worldSize;
    const row = Math.floor(snakeIdx / worldSize);

    ctx.beginPath();

    ctx.fillRect(
      col * CELL_SIZE,
      row * CELL_SIZE,
      CELL_SIZE,
      CELL_SIZE,
    );

    ctx.stroke();
  }

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
