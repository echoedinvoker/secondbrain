---
date: 2025-01-09
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Start the game

In [[2025-01-09_Simple-UI|this topic]], we already created a button with id in HTML. Now we want to make it clickable and do change the world status to **Played**.

In Rust, we do not have any method yet to change the status of World to Played, so we need to implement a method first.

```rs
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

    // create a simple method just to change the status of the world to Played
    pub fn start_game(&mut self) {
        self.status = Some(GameStatus::Played);
    }

    // ...
}
```

Then, we can use this method in our JavaScript code.

```ts
import init, { World, Direction } from "snake_game";
import { rnd } from "./utils/rnd";

init().then(wasm => {

  // ...

  // get the button element
  const button = document.getElementById("game-control-btn");

  // when the button is clicked, start the game by calling the start_game method of World in RUST
  button.addEventListener("click", () => {
    world.start_game();
  })

  // ...

  function paint() {
    drawWorld();
    drawSnake();
    drawRewardCell();
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

Now, the user can start the game by clicking the button. The game will start and the snake will move.

But, the function `update` is calling when the game mounts. We can change it to start the game only when the game is played, which is more efficient.

```ts
import init, { World, Direction } from "snake_game";
import { rnd } from "./utils/rnd";

init().then(wasm => {

  // ...

  const button = document.getElementById("game-control-btn");

  button.addEventListener("click", () => {
    world.start_game();

    play(); // instead of calling the `update` function when the game mounts, call it only when the user clicks the button

  })

  // ...

  function play() {
  //       ^^^^ rename the function from `update` to `play`
    setTimeout(() => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      world.step();
      paint();
      requestAnimationFrame(play)
      //                    ^^^^
    }, 1000 / FPS)
  }

  paint()
  // update()  // do not call the update function when the game mounts, call it only when the user clicks the button
})

```

But there is a problem, when users click the button again, the game will create another setTimeout and the game will be faster. We'll fix it in the next topic.



