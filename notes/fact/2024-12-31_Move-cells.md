---
date: 2024-12-31
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Move cells

In [[2024-12-29_Refactor-snake-update|this topic]], we've already implemented the movement of the snake whole body, but lecturer use another logic to update the snake body as below:

```rs
// src/lib.rs

// ...

#[wasm_bindgen]
#[derive(Clone)]  // add `Clone` trait to `SnakeCell` so that we can clone it
pub struct SnakeCell(usize);

// ...

#[wasm_bindgen]
impl World {

    // ...

    pub fn snake_head_idx(&self) -> usize {
        self.snake.body[0].0
    }

    // ...

    pub fn gen_next_snake_head(&self) -> SnakeCell {
        let snake_idx = self.snake_head_idx();

        let row = snake_idx / self.width;

        let world_size = self.width * self.width;

        match self.snake.direction { ... }
    }
    pub fn step(&mut self) {
        // **Our original logic to update the snake body**
        // let new_head = self.gen_next_snake_head();
        // self.snake.body.insert(0, new_head);
        // self.snake.body.pop();

        // **Lecturer's logic to update the snake body**
        let temp = self.snake.body.clone();  // clone the snake body before any cell is updated
                                            // clone need to be implemented for SnakeCell by adding `Clone` trait through `#[derive(Clone)]`
        self.snake.body[0] = self.gen_next_snake_head(); // update snake head's position (index)
        (1..self.snake.body.len()).for_each(|i| self.snake.body[i].0 = temp[i - 1].0); // iterate through the rest of the snake body
      // ^ not from 0 because the head is already updated                              // update their position with the cloned snake body position
    }
}
```

Although cloning in RUST still requires extra memory space, the memory locations of the snake cells used for rendering have not changed, so there is no need to reacquire pointer positions in JS.

```ts
// www/index.ts

import init, { World, Direction } from "snake_game";

init().then(wasm => {
  const FPS = 10;
  const CELL_SIZE = 10;
  const WORLD_WIDTH = 16;
  const spawn_snake_head_idx = Date.now() % (WORLD_WIDTH * WORLD_WIDTH);
  const world = World.new(WORLD_WIDTH, spawn_snake_head_idx, 3);

  const worldSize = world.width();

  const canvas = <HTMLCanvasElement>document.getElementById("snake-canvas");
  canvas.width = worldSize * CELL_SIZE;
  canvas.height = worldSize * CELL_SIZE;
  const ctx = canvas.getContext("2d");

  document.addEventListener("keydown", e => { ... })


  function drawWorld() { ... }

  // move these two lines out of the drawSnake function
  const snakeCellPtr = world.snake_cells();
  const snakeLength = world.snake_cells_len();

  function drawSnake() {
    // const snakeCellPtr = world.snake_cells();  // we don't need to reacquire the pointer and the length of the snake body every time we update the snake body anymore
    // const snakeLength = world.snake_cells_len();
    const snakeCells = new Uint32Array(
      wasm.memory.buffer,
      snakeCellPtr,
      snakeLength
    )

    snakeCells.forEach((cellIdx, index) => { ... })

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
      paint();
      requestAnimationFrame(update)
    }, 1000 / FPS)
  }

  paint()
  update()
})

```

The snake body should work as expected with the new logic as well.
