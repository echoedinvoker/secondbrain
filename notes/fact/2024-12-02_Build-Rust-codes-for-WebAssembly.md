---
date: 2024-12-02
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Build Rust codes for WebAssembly

**1. Create a new Rust project for library**

```bash
cargo new --lib snake_game
#         ^^^^^ for library, so it won't create main.rs but lib.rs
cd snake_game/
```

**2. Edit `Cargo.toml`**

```toml
[package]
name = "snake_game"
version = "0.1.0"
edition = "2021"

[dependencies]
wasm-bindgen = "0.2.63" # dependency for WebAssembly

[lib]
crate-type = ["cdylib"] # let compiler know it's for integration with external languages

```

**3. Create a new function in `src/lib.rs`**

```rust
use wasm_bindgen::prelude::*;

#[wasm_bindgen] // this attribute is required for the function to be exposed to JavaScript
pub fn greet(name: &str) {
    println!("Hello, {}!", name);
}

```

**4. Install `wasm-pack` and build the project for WebAssembly with it**

```bash
cargo install wasm-pack
# it takes a while to install...

wasm-pack build --target web
# compile the project to WebAssembly package

tree pkg/ # output directory
pkg/
├── package.json
├── snake_game_bg.wasm  # 44KB, quite large, we will reduce it later...
├── snake_game_bg.wasm.d.ts
├── snake_game.d.ts
└── snake_game.js  # JavaScript glue code
```


If there is some wasm-opt error, check [[2024-12-03_If-we-got-error-when-wasm-pack-build|this topic]].
