---
date: 2025-01-10
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Handle game status

In [[2025-01-09_Start-the-game|this topic]], we implemented the functionality to start the game. But if users click the start button again, it will faster the game speed. We need to handle the game status to prevent this.

```rs

// ...

#[wasm_bindgen]
#[derive(Clone, Copy)]  // 3) Add the Clone and Copy traits to the GameStatus enum to make it copyable
pub enum GameStatus {
    Won,
    Lost,
    Played,
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

    // 1) Add a method to get the status of the game, we'll use this in JS
    pub fn get_status(&self) -> Option<GameStatus> {
        self.status // 2) this will move the status out of the struct, but `self` here is a reference
                    //    so it MUST be a copy
    }

   // ...
}
```

Then, we can use it in JS to only start the game if it hasn't been started yet:

```ts
// ...

init().then(wasm => {

  // ...

  button.addEventListener("click", () => {
    if (!world.get_status()) {  // Rust None ---> JS undefined
                                // Rust enum inside Some will turn into JS number, starting from 0 (if enum variants are not associated with values)
                                // So `Some(GameStatus::Played)` will be number 2 in JS
      world.start_game(); // only start if game is not already started yet
      play();
    } else {
      location.reload(); // otherwise reload the page
    }
  })

  // ...

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

