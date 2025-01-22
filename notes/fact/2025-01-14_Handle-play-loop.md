---
date: 2025-01-14
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Handle play loop

```ts
// www/index.ts

import init, { World, Direction } from "snake_game";
import { rnd } from "./utils/rnd";

init().then(wasm => {

  // ...

  function play() {
    setTimeout(() => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      world.step();
      paint();
      if (world.get_status() !== 2) return
      //                         ^ not good for readability
      requestAnimationFrame(play)
    }, 1000 / FPS)
  }

  paint()
})

```

We want to fix above readability issue and change play button text to `Re-play` when game is over.

```ts
import init, { World, Direction, GameStatus } from "snake_game";
import { rnd } from "./utils/rnd";

init().then(wasm => {

  // ...

  const button = document.getElementById("game-control-btn");

  // ...

  function play() {
    if (world.get_status() !== GameStatus.Played) {
      //                       ^^^^^^^^^^^^^^^^^ even we know `GameStatus.Played === 2`, but better to use enum instead of number
      button.textContent = "Re-Play"; // change button text when game is over
      return; // prevent continue to call the play() 
    }
    setTimeout(() => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      world.step();
      paint();
      // if (world.get_status() !== 2) return
      requestAnimationFrame(play)
    }, 1000 / FPS)
  }

  paint()
})
```
