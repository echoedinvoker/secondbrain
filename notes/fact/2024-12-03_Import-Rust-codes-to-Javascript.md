---
date: 2024-12-03
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Import Rust codes to Javascript

We move previous project into snake_game project like this:

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
└── www  # rename previous project to www and move it into snake_game
    ├── index.html
    ├── index.js
    ├── package.json
    ├── public
    │   └── sum.wasm  # before we extract fn from sum.wasm into index.js
    │                 # now we want to extract fn from ..pkg files
    └── webpack.config.js
```

Check the name of rust project in `pkg/package.json`:

```json
{
  "name": "snake_game",
        ^^^^^^^^^^ # this is the name of rust project
  "type": "module",
  "version": "0.1.0",
  "files": [
    "snake_game_bg.wasm",
    "snake_game.js",
    "snake_game.d.ts"
  ],
  "main": "snake_game.js",
  "types": "snake_game.d.ts",
  "sideEffects": [
    "./snippets/*"
  ]
}
```

Then, we can import the rust project into javascript project in `www/package.json`:

```json
{
  "name": "init",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "dev": "webpack-dev-server",
    "build": "webpack build"
  },
  "keywords": [],
  "author": "Matt Chang",
  "license": "ISC",
  "description": "",
  "dependencies": {
    "copy-webpack-plugin": "^12.0.2",
    "webpack": "^5.96.1",
    "webpack-cli": "^5.1.4",

    "snake_game": "file:../pkg"
    ^^^^^^^^^^^^^^^^^^^^^^^ # import rust project by its name and its path

  },
  "devDependencies": {
    "webpack-dev-server": "^5.1.0"
  }
}
```

Now, we can use it in our javascript project:

```js
// www/index.js
import init, { greet } from "snake_game";

init().then(_ => {  // init is async, we need to use it to init the wasm
                    // then we can use the fn we created in rust
  greet("World");
})
```

```bash
~/D/g/s/w/snake_game > cd www
~/D/g/s/w/s/www > npm i  # because we have new dependency `snake_game`
~/D/g/s/w/s/www > npm run dev

```

We can open http://localhost:8080/ in the browser to see the results, but using `println!` in RUST code will not display in the browser console, so as long as no errors occur, it is fine.
```
