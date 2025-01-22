---
date: 2024-12-26
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Convert index.js to index.ts

Because Rust itself is a typed language, and the files built also provide a d.ts file, so we can change index.js to index.ts to get more help with type checking.

## Setup TypeScript to Webpack

Basically follow the tutorial of [Webpack - Typescript](https://webpack.js.org/guides/typescript/), but with some minor adjustments.

```bash
~/D/g/s/w/snake_game > cd www
~/D/g/s/w/s/www > npm install --save typescript ts-loader
#                             ^^^^^^ document using --save-dev but lecturer turn it to --save, I don't know why
```

Create a `tsconfig.json` file:

```json
~/D/g/s/w/s/www > cat tsconfig.json
{
  "compilerOptions": {
    "outDir": "./public/",
    //        ^^^^^^ document use "dist", need to change to "public" for our case
    "noImplicitAny": true,
    "module": "es6",
    "target": "es5",
    // "jsx": "react",  // we don't use react framework, so remove this line
    "allowJs": true,
    "moduleResolution": "node"
  }
}
```

Copy paste the content from the document into `webpack.config.js`

```js
// webpack.config.js

const path = require('path');
const CopyWebpackPlugin = require('copy-webpack-plugin');

module.exports = {
  entry: './bootstrap.js',
  output: {
    path: path.resolve(__dirname, 'public'),
    filename: 'bootstrap.js'
  },
  mode: 'development',

  // copy paste here
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
  resolve: {
    extensions: ['.tsx', '.ts', '.js'],
  },
  // end

  plugins: [
    new CopyWebpackPlugin({
      patterns: [
        { from: './index.html', to: './' },
      ]
    })
  ]
}
```


## Turn index.js to index.ts

```js
// bootstrap.js 

import('./index')
    //    ^^^^^ remove .js
  .catch(e => console.error('Error importing `index.js`:', e));

```

Rename `index.js` to `index.ts`

```bash
~/D/g/s/w/s/www > tree -I 'node_modules'
.
├── bootstrap.js
├── index.html
├── index.ts  # rename
├── package.json
├── package-lock.json
├── public
│   ├── export_file.xlsx
│   └── sum.wasm
├── tsconfig.json
└── webpack.config.js
```

Now, insides `index.ts`, we can find some error because of the type checking. Let's fix it.

```ts
// index.ts 
import init, { World } from "snake_game";

init().then(_ => {
  const CELL_SIZE = 10;
  const world = World.new(8, 10);

  console.log(world.snake_head_idx());

  const worldSize = world.width();

  const canvas = <HTMLCanvasElement>document.getElementById("snake-canvas");
  //             ^^^^^^^^^^^^^^^^^^^ because default type from `getElementById` is `HTMLElement` only
  //                                 we need to cast it to `HTMLCanvasElement`
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
  }

  paint()
  update()
})

```

