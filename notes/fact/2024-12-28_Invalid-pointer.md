---
date: 2024-12-28
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Invalid pointer

In [[2024-12-28_Lecturer-solution-for-getting-snake-cells|this topic]], we use `raw pointer` to get the reference of the snake body from JS to wasm module memory. But, if we mutate the vector in RUST, the pointer may be invalid anymore.

```rs
// src/lib.rs

// ...

#[wasm_bindgen]
impl World {

    // ... 

    pub fn snake_cells(&self) -> *const SnakeCell {
        self.snake.body.as_ptr()
    }
    pub fn snake_cells_len(&self) -> usize {
        self.snake.body.len()
    }

    // create a new function to mutate the snake body
    pub fn oopsie(&mut self) {
        self.snake.body[0].0 = 0;
    }
}

```
```ts
// www/index.ts

// ...

init().then(wasm => {

  // ...

  const snakeCellPtr = world.snake_cells();
  const snakeLength = world.snake_cells_len();

  const snakeCells = new Uint32Array(
    wasm.memory.buffer,
    snakeCellPtr,
    snakeLength
  )

  console.log(snakeCells)

  // mutate the snake body in RUST
  world.oopsie()

  console.log(snakeCells) // check if snakeCells has changed

  // ...
})

```

```bash
Uint32Array(3) [4, 5, 6, buffer: ArrayBuffer(1179648), byteLength: 12, byteOffset: 1179636, length: 3, Symbol(Symbol.toStringTag): 'Uint32Array']
Uint32Array(3) [0, 5, 6, buffer: ArrayBuffer(1179648), byteLength: 12, byteOffset: 1179636, length: 3, Symbol(Symbol.toStringTag): 'Uint32Array']
#               ^ first element is changed as expected

```

In above case, the raw pointer works as expected in the first and second call, but if we change the mutate way as below:

```rs
// src/lib.rs

// ...

#[wasm_bindgen]
impl World {

    // ... 

    pub fn snake_cells(&self) -> *const SnakeCell {
        self.snake.body.as_ptr()
    }
    pub fn snake_cells_len(&self) -> usize {
        self.snake.body.len()
    }

    pub fn oopsie(&mut self) {
        self.snake.body = vec![SnakeCell(0)];
        //             ^^^^^^^^^^^^^^^^^^^^^^ instead of mutating the first element, mutate the whole vector this time
    }
}

// ...

```

```bash
Uint32Array(3) [41, 42, 43, buffer: ArrayBuffer(1179648), byteLength: 12, byteOffset: 1179636, length: 3, Symbol(Symbol.toStringTag): 'Uint32Array']
Uint32Array(3) [1171456, 42, 43, buffer: ArrayBuffer(1179648), byteLength: 12, byteOffset: 1179636, length: 3, Symbol(Symbol.toStringTag): 'Uint32Array']
#               ^^^^^^^^^^^^^^^ first element is not 0, and there shouldn't be the second and third elements

```

That's because the pointer is not valid anymore after the vector is reallocated. To fix this, we must re-get the pointer and length after the vector is reallocated in JS.

```ts
// ...

init().then(wasm => {
  // ...

  const snakeCellPtr = world.snake_cells();
  const snakeLength = world.snake_cells_len();

  const snakeCells = new Uint32Array(
    wasm.memory.buffer,
    snakeCellPtr,
    snakeLength
  )

  console.log(snakeCells)

  world.oopsie()  // this mutation will reallocate the vector, so the old pointer is not valid anymore

  // re-get the pointer and length
  const snakeCellPtr2 = world.snake_cells();
  const snakeLength2 = world.snake_cells_len();

  // recreate the Uint32Array
  const snakeCells2 = new Uint32Array(
    wasm.memory.buffer,
    snakeCellPtr2,
    snakeLength2
  )

  console.log(snakeCells2)

  // ...

})
```

```bash
Uint32Array(3) [13, 14, 15, buffer: ArrayBuffer(1179648), byteLength: 12, byteOffset: 1179636, length: 3, Symbol(Symbol.toStringTag): 'Uint32Array']
Uint32Array [0, buffer: ArrayBuffer(1179648), byteLength: 4, byteOffset: 1163244, length: 1, Symbol(Symbol.toStringTag): 'Uint32Array']
#            ^ first element is changed as expected, and only one element exists in the array

```
