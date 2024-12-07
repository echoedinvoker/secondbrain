---
date: 2024-12-03
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# If we got error when wasm pack build

In [[2024-12-02_Build-Rust-codes-for-WebAssembly|this topic]], when we build Rust codes for WebAssembly with `wasm-pack`, we may get error like this:


```bash

wasm-pack build --target web

# there are some warnings about wasm-opt...

tree pkg/ # output directory
pkg/
├── package.json  # this file may be not created successfully
├── snake_game_bg.wasm 
├── snake_game_bg.wasm.d.ts
├── snake_game.d.ts
└── snake_game.js 
```

In this situation, we can try to fix it by adding some configurations in `Cargo.toml`:

```toml
[package]
name = "snake_game"
version = "0.1.0"
edition = "2021"

[dependencies]
wasm-bindgen = "0.2.63"

[lib]
crate-type = ["cdylib"]

# add this part to avoid wasm optimization check
[package.metadata.wasm-pack.profile.release]
wasm-opt = false

```
