---
date: 2024-12-27
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Keydown events

In [[2024-12-27_Refactor-update-function-for-readability|this topic]], we finished the snake's movement in different directions by Rust.

Now, we need to handle the keydown events in JavaScript to control the snake. This topic just focuses on the keydown events only.

```ts
// www/index.ts

import init, { World } from "snake_game";

init().then(_ => {
  const FPS = 10;
  const CELL_SIZE = 10;
  const WORLD_WIDTH = 8;
  const spawn_snake_head_idx = Date.now() % (WORLD_WIDTH * WORLD_WIDTH);
  const world = World.new(WORLD_WIDTH, spawn_snake_head_idx);

  const worldSize = world.width();

  const canvas = <HTMLCanvasElement>document.getElementById("snake-canvas");
  canvas.width = worldSize * CELL_SIZE;
  canvas.height = worldSize * CELL_SIZE;
  const ctx = canvas.getContext("2d");

  // Add a listener for keydown events
  document.addEventListener("keydown", e => {
    switch (e.code) {
    //      ^^^^^^ key code is a string for the pressed key
      case "ArrowUp":
        console.log("up");
        break;
      case "ArrowRight":
        console.log("right");
        break;
      case "ArrowDown":
        console.log("down");
        break;
      case "ArrowLeft":
        console.log("left");
        break;
    }
  })  // just test them on the browser console


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
    }, 1000 / FPS)
  }

  paint()
  update()
})

```

After ensuring that every keydown event is triggered and displaying the correct message in console.log(), we can proceed to the next step - [[2024-12-27_Change-snake-dir|change snake dir]].
