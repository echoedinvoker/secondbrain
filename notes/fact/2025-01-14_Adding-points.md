---
date: 2025-01-14
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Adding points

We want to add a points system to our game, which means we need to add one more field `points` to our World struct in Rust.

```rs
// src/lib.rs

// ...

#[wasm_bindgen]
pub struct World {
    width: usize,
    snake: Snake,
    next_cell: Option<SnakeCell>,
    reward_cell: Option<usize>,
    status: Option<GameStatus>,
    points: usize, // <- new field
}

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize, size: usize) -> Self {
        let body: Vec<SnakeCell> = (0..size).map(|x| SnakeCell(spwan_idx + x)).collect();

        World {
            width,
            reward_cell: Self::gen_reward_cell(width, &body),
            snake: Snake {
                body,
                direction: Direction::Up,
            },
            next_cell: None,
            status: None,
            points: 0, // init it to 0
        }
    }

    // ...

    // write a getter for JS to access the points
    pub fn get_points(&self) -> usize {
        self.points
    }

    // ...
  
    pub fn step(&mut self) {
        if let Some(GameStatus::Played) = self.status {
            let temp = self.snake.body.clone();
            match &self.next_cell {
                Some(cell) => {
                    self.snake.body[0] = cell.clone();
                    self.next_cell = None;
                }
                None => self.snake.body[0] = self.gen_next_snake_head(self.snake.direction),
            };

            (1..self.snake.body.len()).for_each(|i| self.snake.body[i].0 = temp[i - 1].0);

            if self.snake.body[1..self.snake.body.len()].contains(&self.snake.body[0]) {
                self.status = Some(GameStatus::Lost);
            }

            if let Some(reward_cell) = self.reward_cell {
                if reward_cell == self.snake_head_idx() {

                    self.points += 1;  // increment the points when snake eats the reward cell

                    self.snake.body.push(SnakeCell(self.snake.body[1].0));
                    if self.snake_cells_len() < self.width * self.width {
                        self.reward_cell = Self::gen_reward_cell(self.width, &self.snake.body);
                    } else {
                        self.reward_cell = None;
                        self.status = Some(GameStatus::Won);
                    }
                }
            }
        }
    }
}
```

Before accessing the points in JS, we add a segment of HTML preparing the UI for the points.

```html
<!-- www/index.html  -->

<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    /* ... */
  </style>
</head>


<body>
  <div class="container">

    <div class="game-panel">
      <div class="flex">
        <div class="label">Status:</div>
        <div id="game-status">None</div>
      </div>

      <!-- Just copy from game-panel to create a new segment for points -->
      <div class="flex">
        <div class="label">Points:</div>
        <div id="game-points"></div>
            <!-- ^^^^^^^^^^^ remember change the id -->
      </div>

      <button id="game-control-btn">Play</button>
    </div>

    <canvas id="snake-canvas"></canvas>
  </div>
  <script src="./bootstrap.js"></script>
</body>
```

Then, we can access the points in JS and present it in the UI.

```ts
// www/index.ts 

import init, { World, Direction } from "snake_game";
import { rnd } from "./utils/rnd";

init().then(wasm => {

  // ...

  // get the element that we just prepared for the points
  const points = document.getElementById("game-points");

  // ...

  // draw the points is similar to draw the status, so we don't create new draw function
  function drawGameStatus() {
    status.textContent = world.get_status_text();
    points.textContent = world.get_points().toString(); // get the points from Rust and present it
    //                                     ^^^^^^^^^^^ because the points type is number, need to convert it to string
  }

  // ...

})

```
