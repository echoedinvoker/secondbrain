---
date: 2025-01-10
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Draw game status

In [[2025-01-10_Handle-game-status|this topic]], we implemented the method to get the game status but the status text in the game is not updated. In this topic, we will implement the method to draw the game status on the screen.


![game-status.png](../assets/imgs/game-status.png)

The method we implemented in the previous topic will return `undefined` or number, but we need to draw the status text on the screen. So we are going to implement a new method to return the text based on the status in Rust.

```rs

// ...

#[wasm_bindgen]
#[derive(Clone, Copy)]
pub enum GameStatus {
    Won,
    Lost,
    Played,
}

struct Snake {
    body: Vec<SnakeCell>,
    direction: Direction,
}

#[wasm_bindgen]
pub struct World {
    width: usize,
    snake: Snake,
    next_cell: Option<SnakeCell>,
    reward_cell: usize,
    status: Option<GameStatus>,
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
        }
    }

    // ...

    pub fn start_game(&mut self) {
        self.status = Some(GameStatus::Played);
    }

    pub fn get_status(&self) -> Option<GameStatus> {
        self.status
    }

    // create a new method to get the status text based on the status
    pub fn get_status_text(&self) -> String {

        // status is an Option<GameStatus>, which is a nested enum, so we need to match it on all the possible
        match self.status {
            Some(GameStatus::Won) => String::from("You have won!"),
            Some(GameStatus::Lost) => String::from("You have lost!"),
            Some(GameStatus::Played) => String::from("Playing"),
            None => String::from("No Status"),
        }
    }

    // ...

}
```

Then, we need to draw the game status in JS with it.

```ts
import init, { World, Direction } from "snake_game";
import { rnd } from "./utils/rnd";

init().then(wasm => {
  const FPS = 2;
  const CELL_SIZE = 10;
  const WORLD_WIDTH = 4;
  const spawn_snake_head_idx = rnd(WORLD_WIDTH * WORLD_WIDTH);
  const world = World.new(WORLD_WIDTH, spawn_snake_head_idx, 3);

  const worldSize = world.width();

  // get the element which we want to update the game status
  const status = document.getElementById("game-status");

  const button = document.getElementById("game-control-btn");

  const canvas = <HTMLCanvasElement>document.getElementById("snake-canvas");
  canvas.width = worldSize * CELL_SIZE;
  canvas.height = worldSize * CELL_SIZE;
  const ctx = canvas.getContext("2d");

  button.addEventListener("click", () => { ... })

  document.addEventListener("keydown", e => { ... })


  function drawWorld() { ... }
  function drawRewardCell() { ... }

  // create a new function to draw the game status
  function drawGameStatus() {
    status.textContent = world.get_status_text();  // use Rust method to get the status text and update the element
  }

  function paint() {
    drawWorld();
    drawSnake();
    drawRewardCell();
    drawGameStatus(); // draw the game status
  }

  function play() {
    setTimeout(() => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      world.step();
      paint();
      requestAnimationFrame(play)
    }, 1000 / FPS)
  }

  paint()
})
```

![no-status.png](../assets/imgs/no-status.png)

![playing-game.png](../assets/imgs/playing-game.png)


