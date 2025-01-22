---
date: 2024-12-27
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Random spawn snake head

```ts
// www/index.ts 

import init, { World } from "snake_game";

init().then(_ => {
  const CELL_SIZE = 10;
  const world = World.new(8, 10);
  //                      ^^^^^ we want to extract width as CONSTANT for more readability
  //                            and make spawn snake head index random

  const worldSize = world.width();

  const canvas = <HTMLCanvasElement>document.getElementById("snake-canvas");
  canvas.width = worldSize * CELL_SIZE;
  canvas.height = worldSize * CELL_SIZE;

  const ctx = canvas.getContext("2d");

  function drawWorld() { ... }

  function drawSnake() { ... }

  function paint() {
    drawWorld();
    drawSnake();
  }

  function update() {
    setTimeout(() => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      world.update();
      paint();
      requestAnimationFrame(update)
    }, 100)
    // ^^^ we want to extract 100 as CONSTANT for setting fps
  }

  paint()
  update()
})

```

We want to do some refactor as above comments. 

```ts
// www/index.ts

import init, { World } from "snake_game";

init().then(_ => {
  const CELL_SIZE = 10;
  const WORLD_WIDTH = 8;  // extract width as CONSTANT
  const spawn_snake_head_idx = Date.now() % (WORLD_WIDTH * WORLD_WIDTH);
  //                           ^^^^^^^^^^ this will get number such as 1723941, so need to make sure it's in range of 0 ~ WORLD_WIDTH * WORLD_WIDTH
  const world = World.new(WORLD_WIDTH, spawn_snake_head_idx);
  //                      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  const worldSize = world.width();

  const canvas = <HTMLCanvasElement>document.getElementById("snake-canvas");
  canvas.width = worldSize * CELL_SIZE;
  canvas.height = worldSize * CELL_SIZE;

  const ctx = canvas.getContext("2d");

  function drawWorld() { ... }

  function drawSnake() { ... }

  function paint() {
    drawWorld();
    drawSnake();
  }

  const FPS = 10;  // add FPS as CONSTANT

  function update() {
    setTimeout(() => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      world.update();
      paint();
      requestAnimationFrame(update)
    }, 1000 / FPS)
    // ^^^^^^^^^^ FPS means frame per second, so we need to divide 1000 by FPS
  }

  paint()
  update()
})

