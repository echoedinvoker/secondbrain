---
date: 2024-12-07
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Change memory allocator used to build wasm file

In [[2024-12-02_Build-Rust-codes-for-WebAssembly|this topic]] we build Rust codes for WebAssembly with `wasm-pack`, but we found that the size of .wasm file is a bit too large. We can try to change the memory allocator from Rust's default global allocator to `wee_alloc` to reduce the size of the .wasm file.

**1. Check the size of the built package**

```bash
~/D/g/s/w/snake_game > du -h pkg
48K     pkg
~/D/g/s/w/snake_game > du -h pkg/*
4.0K    pkg/package.json
4.0K    pkg/snake_game.d.ts
8.0K    pkg/snake_game.js
20K     pkg/snake_game_bg.wasm  # 20KB, quite large...
4.0K    pkg/snake_game_bg.wasm.d.ts

```

**2. Install `wee_alloc` package**

```toml
<!--Cargo.toml-->
[package]
name = "snake_game"
version = "0.1.0"
edition = "2021"

[dependencies]
wasm-bindgen = "0.2.63"
wee_alloc = "0.4.5" # add this line, Rust will start to install when save this file

[lib]
crate-type = ["cdylib"]

[package.metadata.wasm-pack.profile.release]
wasm-opt = false

```

**3. Change the allocator**

```rust
// src/lib.rs

use wasm_bindgen::prelude::*;

// change the global allocator to wee_alloc
#[global_allocator] // this attribute is required, don't forget it
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;
//     ^^^^^ basically, we just changed the value and type of the static variable `ALLOC`

#[wasm_bindgen]
pub fn greet(name: &str) {
    alert(name);
}

#[wasm_bindgen]
extern {
    pub fn alert(s: &str);
}
```

**4. Rebuild the project**

```bash
~/D/g/s/w/snake_game > wasm-pack build --target web

~/D/g/s/w/snake_game > du -h pkg
32K     pkg

~/D/g/s/w/snake_game > du -h pkg/*
4.0K    pkg/package.json
4.0K    pkg/snake_game.d.ts
8.0K    pkg/snake_game.js
4.0K    pkg/snake_game_bg.wasm  # reduced to 4KB from 20KB
4.0K    pkg/snake_game_bg.wasm.d.ts

```

Replacing with wee_alloc is recommended in the official Rust WebAssembly documentation. There are many recommendations in the official documentation, and this is just one of them. The package repo for wee_alloc can be found [here](https://github.com/rustwasm/wee_alloc), feel free to check it out if you are interested.
