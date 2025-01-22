---
date: 2024-12-11
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Replace accessing struct field with getter fn

In [[2024-12-11_Create-World-of-snake-game-by-Rust|this topic]], we create World struct for snake game, and publish its field `width` directly. But it's not a good practice to access struct field directly, we should use getter function instead.


```rs
// src/lib.rs

use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

#[wasm_bindgen]
pub struct World {
    width: usize  // remove `pub` prefix to make it private
}                 // so that JS cannot access this field directly now

#[wasm_bindgen]
impl World {
    pub fn new(width: usize) -> Self {
        World { width }
    }

    // add getter function to access the field `width`, this is better practice
    pub fn width(&self) -> usize {
        self.width
    }
}

```

```bash
~/D/g/s/w/snake_game > wasm-pack build --target web
```

```js
~/D/g/s/w/snake_game > cat www/index.js
import init, { World } from "snake_game";

init().then(_ => {
  const world = World.new(4);
  console.log(world.width());
  //                     ^^ instead of accessing field directly, we use getter function now
})
```

```bash
~/D/g/s/w/snake_game > cd www
~/D/g/s/w/s/www > npm run dev # we should see 4 printed in console of browser

```
