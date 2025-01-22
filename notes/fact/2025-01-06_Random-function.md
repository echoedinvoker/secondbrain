---
date: 2025-01-06
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Random function

In [[2025-01-05_Import-Date-to-Rust|this topic]], we used the `Date.now()` to spawn the reward cell randomly, but it's the same position with the snake head, so we need to use other random function to handle this.

```bash
~/D/g/s/w/snake_game > tree www/utils/
www/utils/
└── rnd.js
#   ^^^ because we don't use the Date.now to generate random number anymore,
#       so better to rename the file to rnd.js

```

```js
~/D/g/s/w/snake_game > cat www/utils/rnd.js 
// export const now = Date.now;

// we use Math.random() to generate random number
export function rnd(max) {
  //                ^^^ the max number should be the world size (width * width)
  return Math.floor(Math.random() * max);
}

```

We can use it in both Rust and JS.

```ts
~/D/g/s/w/snake_game > cat www/index.ts
import init, { World, Direction } from "snake_game";
import { rnd } from "./utils/rnd";

init().then(wasm => {
  const FPS = 10;
  const CELL_SIZE = 10;
  const WORLD_WIDTH = 16;
  // const spawn_snake_head_idx = Date.now() % (WORLD_WIDTH * WORLD_WIDTH);
  const spawn_snake_head_idx = rnd(WORLD_WIDTH * WORLD_WIDTH);
  //                           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ replace Date.now() with rnd function

  // ...

})
```

```rs
// src/lib.rs

use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

#[wasm_bindgen(module = "/www/utils/rnd.js")]
//                                  ^^^
extern "C" {
    fn rnd(max: usize) -> usize;
//  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^
}

// ...

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize, size: usize) -> Self {
        // let reward_cell = now( % (width * width);
        let reward_cell = rnd(width * width);
        //                ^^^^^^^^^^^^^^^^^^ replace now() with rnd(max)
        World {
            width,
            snake: Snake {
                body: (0..size).map(|x| SnakeCell(spwan_idx + x)).collect(),
                direction: Direction::Up,
            },
            next_cell: None,
            reward_cell,
        }
    }

    // ...

}
```

Let's test it.

![rnd-rs-js.png](../assets/imgs/rnd-rs-js.png)

You can see the reward cell is not the same position with the snake head in the above image. But there is still a tiny chance that the reward cell is the same position with the snake head or the snake body, so we need to handle this in the next topic.

