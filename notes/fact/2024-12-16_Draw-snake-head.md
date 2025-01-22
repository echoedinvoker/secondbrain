---
date: 2024-12-16
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Draw snake head

In [[2024-12-16_Create-Snake-in-the-World|this topic]], we create a struct Snake in the World module and provides a method to access the snake head index. Now, we will draw the snake head on the canvas in JS.

```js
// www/index.js

import init, { World } from "snake_game";

init().then(_ => {
  const CELL_SIZE = 10;
  const world = World.new(8, 10);

  console.log(world.snake_head_idx());

  const worldSize = world.width();

  const canvas = document.getElementById("snake-canvas");
  canvas.width = worldSize * CELL_SIZE;
  canvas.height = worldSize * CELL_SIZE;

  const ctx = canvas.getContext("2d");

  function drawWorld() {
    ctx.beginPath();

    for (let x = 0; x < worldSize + 1; x++) {
      ctx.moveTo(x * CELL_SIZE, 0);
      ctx.lineTo(x * CELL_SIZE, worldSize * CELL_SIZE);
    }

    for (let y = 0; y < worldSize + 1; y++) {
      ctx.moveTo(0, y * CELL_SIZE);
      ctx.lineTo(worldSize * CELL_SIZE, y * CELL_SIZE);
    }

    ctx.stroke();
  }

  // like drawWorld() above, here we create another function to draw the snake head
  function drawSnake() {
    const snakeIdx = world.snake_head_idx();  // get the snake head index

    // calculate the row and column index of the snake head (left-top corner of the snake head cell)
    const col = snakeIdx % worldSize;
    const row = Math.floor(snakeIdx / worldSize);

    ctx.beginPath(); // same as drawWorld(), we write the drawing logic between beginPath() and stroke()

    // but instead of lineTo, now we need to use fillRect to draw the square of the snake head
    ctx.fillRect(
      col * CELL_SIZE,  // x position of the left-top corner of the snake head cell
      row * CELL_SIZE,  // y position of the left-top corner of the snake head cell
      CELL_SIZE,  // width of the snake head cell
      CELL_SIZE,  // height of the snake head cell
    );

    ctx.stroke();
  }

  drawWorld();
  drawSnake();  // don't forget to call drawSnake()
})

```

![draw-snake-head.png](../assets/imgs/draw-snake-head.png)

