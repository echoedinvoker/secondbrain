---
date: 2024-12-03
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Use bootstrap.js to wrap and load index.js

Up to the [[2024-12-03_Use-JS-fn-in-Rust-code|previous topic]], www always uses index.js as the entry point, but we can create another JS file to wrap and load index.js asynchronous, which will be better for the project's architecture.

If you have used React or Angular, you may be familiar with the concept of bootstrap...

```bash
~/D/g/s/w/snake_game > tree . -I "node_modules|target"
.
├── Cargo.lock
├── Cargo.toml
├── pkg
│   ├── package.json
│   ├── snake_game_bg.wasm
│   ├── snake_game_bg.wasm.d.ts
│   ├── snake_game.d.ts
│   └── snake_game.js
├── src
│   └── lib.rs
└── www
    ├── bootstrap.js # new entry point, use to load index.js async
    ├── index.html
    ├── index.js # previous entry point
    ├── package.json
    ├── package-lock.json
    ├── public
    │   ├── export_file.xlsx
    │   └── sum.wasm
    └── webpack.config.js
```
```js
// www/bootstrap.js 

import('./index.js')  // load index.js async import() is a promise function
  .catch(e => console.error('Error importing `index.js`:', e));
  // in this case, we can catch the error when loading index.js failed
  // so we didn't do too much in bootstrap.js, just a simple import
```

Becuase our entry point is changed, we need to update the webpack.config.js to use bootstrap.js as the entry point.

```js
// www/webpack.config.js 

const path = require('path');
const CopyWebpackPlugin = require('copy-webpack-plugin');

module.exports = {
  entry: './bootstrap.js',
  //        ^^^^^^^^^^^^ change the entry point to bootstrap.js
  output: {
    path: path.resolve(__dirname, 'public'),
    filename: 'bootstrap.js'
  //           ^^^^^^^^^ output filename should also be bootstrap, which more meaningful
  },
  mode: 'development',
  plugins: [
    new CopyWebpackPlugin({
      patterns: [
        { from: './index.html', to: './' },
      ]
    })
  ]
}
```

Because our output filename is changed, we need to update the index.html to use bootstrap.js as the script source.

```html
~/D/g/s/w/snake_game > cat www/index.html 
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <h1>Hello World</h1>
  <script src="./bootstrap.js"></script>
            <!-- ^^^^^^^^^ use bootstrap.js as the script source -->
</body>

</html>
```

