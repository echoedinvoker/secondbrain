---
date: 2024-12-11
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Create canvas for drawing snake game content

In [[2024-12-11_Replace-accessing-struct-field-with-getter-fn|this topic]], we already created a Wrold struct and its getter function for snake game. Now we need to create a canvas to draw the game content.

```html
<!-- www/index.html  -->

<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>

  <!-- styles for div.container to center canvas on the screen -->
  <style>
    .container {
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      position: absolute;
      display: flex;
      align-items: center;
      justify-content: center;
      flex-direction: column;
    }
  </style>
</head>


<body>
  <div class="container"> <!-- to center the canvas on the screen -->
    <canvas id="snake-canvas"></canvas> <!-- we will draw the game content on this canvas -->
  </div>
  <script src="./bootstrap.js"></script>
</body>

```
Then, we need to retrieve the canvas element in JS and get its context to draw on it:

```js
// www/index.js 

import init, { World } from "snake_game";

init().then(_ => {
  const world = World.new(4);
  const canvas = document.getElementById("snake-canvas"); // retrieve the canvas element
  const ctx = canvas.getContext("2d"); // extract 2d context from canvas,
                                       // which provides methods to draw on the canvas
})

```
