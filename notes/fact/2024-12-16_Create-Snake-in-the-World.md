---
date: 2024-12-16
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Create Snake in the World

In [[2024-12-11_Replace-accessing-struct-field-with-getter-fn|this topic]], we finished the World struct, now we want to create a struct `Snake` which also store in the World struct.

We need to store the informations about where the snake is in the world like below concept:

![snake1.png](../assets/imgs/snake1.png)


```rs
// src/lib.rs 

use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

// Create struct Snake to present snake in the world
struct Snake {
    body: Vec<usize>
       // ^^^^^^^^^^ list of cell indexes of snake body
}

#[wasm_bindgen]
pub struct World {
    width: usize,
    snake: Snake  // store snake in the world
}

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize) -> Self {
        World { width, snake: Snake { body: vec![spwan_idx] } }
    }
    pub fn width(&self) -> usize {
        self.width
    }
    // in JS, we need to know which cell the snake head is in
    pub fn snake_head_idx(&self) -> usize {
        self.snake.body[0]
    }
}
```

The Snake body stores the index of the cell, we can create another struct `SnakeCell` to abstract this part, making the code more clear and structured.

```rs
// src/lib.rs

use wasm_bindgen::prelude::*;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

struct SnakeCell(usize);  // create a struct to present a cell of snake
            //  ^^^^^^^ this is tuple struct, only one item
            //          so we can access it by `SnakeCell.0`

struct Snake {
    body: Vec<SnakeCell>
          //  ^^^^^^^^^ replace usize with SnakeCell
}

#[wasm_bindgen]
pub struct World {
    width: usize,
    snake: Snake
}

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize) -> Self {
        World { width, snake: Snake { body: vec![SnakeCell(spwan_idx)] } }
                                              // ^^^^^^^^^^^^^^^^^^^^ replace spwan_idx with SnakeCell(spwan_idx)
    }
    pub fn width(&self) -> usize {
        self.width
    }
    pub fn snake_head_idx(&self) -> usize {
        self.snake.body[0].0
        //                ^^ don't forget to access the first item of SnakeCell
    }
}
```
Now, we can compile the code to wasm for JS:

```bash
wasm-pack build --target web
```

```js
// www/index.js

import init, { World } from "snake_game";

init().then(_ => {
  const CELL_SIZE = 10;
  const world = World.new(8, 10);
  //                         ^^ now we need to provide the spawn index for snake

  console.log(world.snake_head_idx()); // test if we can access snake head index

  // ...(omit)

```

```bash
cd www
npm run dev

# we should see 10 printed in console of browser

```
