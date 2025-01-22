---
date: 2024-12-26
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Refactor updating codes

We want to refactor the codes we written in [[2024-12-26_Update-the-world|this topic]]:

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

  function drawWorld() { ... }

  function drawSnake() { ... }

  // wrap drawWorld and drawSnake into a single function
  function paint() {
    drawWorld();
    drawSnake();
  }


  // wrap setInterval into a function
  function update() {
    setInterval(() => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      world.update();
      paint();   // replace drawWorld and drawSnake with paint

    }, 100)
  }

  // call them once
  paint()
  update()
})

```

Our codes now look cleaner and easier to read. Next, we will use the browser API `requestAnimationFrame` to replace `setInterval` for better performance.


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

  function drawWorld() { ... }

  function drawSnake() { ... }

  function paint() {
    drawWorld();
    drawSnake();
  }

  function update() {
    setTimeout(() => {
  //^^^^^^^^^^ replace setInterval with setTimeout
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      world.update();
      paint();
      requestAnimationFrame(update)  // use `requestAnimationFrame` to call itself
    }, 100)
  }

  paint()
  update()
})
```

Because `requestAnimationFrame` is browser API, it's more efficient than `setInterval` way to update the world. It will only update the world when the browser is ready to paint the next frame, which is more efficient than `setInterval` way.
