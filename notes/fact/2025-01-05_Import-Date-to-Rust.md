---
date: 2025-01-05
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Import Date to Rust

In [[2025-01-05_Init-reward-cell|this topic]], We added a field `reward_cell` to World in Rust but it gets fixed value in `new` method.

In JS, we use `Date.now()` to compute a random value for snake head index as below:

```ts
// www/index.ts
import init, { World, Direction } from "snake_game";

init().then(wasm => {
  const FPS = 10;
  const CELL_SIZE = 10;
  const WORLD_WIDTH = 16;
  const spawn_snake_head_idx = Date.now() % (WORLD_WIDTH * WORLD_WIDTH);
  //                           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  const world = World.new(WORLD_WIDTH, spawn_snake_head_idx, 3);
  //                                   ^^^^^^^^^^^^^^^^^^^^

  // ...

})

```

But in Rust, there is no `Date.now()` function. We need to import `Date.now()` from JS to Rust:

```js
// www/utils/date.js  (create new file)

export const now = Date.now;
//                 ^^^^^^^^ be noted, not `Date.now()` but `Date.now`
//                          because we want to transfer function `value` to Rust, not call it

```

```rs
// src/lib.rs

use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

// Import `now` function from JS
#[wasm_bindgen(module = "/www/utils/date.js")]  // JS -> Rust so need add attribute `wasmbindgen`
//             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ need to tell Rust where the source file is
extern "JavaScript" {  // we use `extern` keyword to declare the foreign function
    fn now() -> usize;  // declare the function signature
}

// ...

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize, size: usize) -> Self {

        // then, we can use `now()` to get random value of `reward_cell`
        let reward_cell = now() % (width * width);

        World {
            width,
            snake: Snake {
                body: (0..size).map(|x| SnakeCell(spwan_idx + x)).collect(),
                direction: Direction::Up,
            },
            next_cell: None,
            reward_cell, // assign the random value instead of fixed value
        }
    }
  
    // ...

}
```

Let's check the result:

![reward-cell-head-same.png](../assets/imgs/reward-cell-head-same.png)

It can be observed that although the position of the reward cell changes every time the game is restarted, it always starts at the same position as the snake head.

This is because both use `Date.now()` to generate a random value, causing them to have the same value. We will address this issue in the next topic.

