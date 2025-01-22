---
date: 2024-12-11
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Create World of snake game by Rust

We will create a struct `World` to represent the world of the game, and implement a `new` method to initialize this struct.

And for programming practice, we should treat the world as a linear array as below:

![world-sg.png](../assets/imgs/world-sg.png)


As figure shown, the world size can be defined only by `width` because the height is equal to the width. And each cell has index from 0.

Continue with the [[2024-12-07_Change-memory-allocator-used-to-build-wasm-file|previous topic's]] codes and remove the demo function `greet`, then we proceed to write World above:


```rs
// src/lib.rs

use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;


// start to define the World struct

#[wasm_bindgen]  // for using in JS, we need to add attribute wasm_bindgen
  pub struct World {
//^^^
      pub width: usize
    //^^^ by default anything in lib is private, so we need to add `pub` to make it public
  }

#[wasm_bindgen]  // for using in JS, we need to add attribute wasm_bindgen
impl World {
    pub fn new(width: usize) -> Self {
  //^^^               ^^^^^ width is always positive integer, so type is unsigned size
        World { width }
    }
}

```

Then, complie the code to .wasm:

```bash
~/D/g/s/w/snake_game > wasm-pack build --target web
```

After compling, we can import the `World` struct to JS, and use its public fn to create World instance:

```js
// www/index.js 

import init, { World } from "snake_game";

init().then(_ => {
  const world = World.new(4);
  console.log(world.width);  // should print 4 to console of browser
})

```
