---
date: 2024-12-03
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Use JS fn in Rust code


In [[2024-12-03_Import-Rust-codes-to-Javascript|this topic]], we use Rust fn `greet` in Javasrcipt but `println!` result won't be shown in console. So we want to replace it with Javascript fn `alert`.

But `alert` only exits in JS, so we need to use `extern` keyword in Rust code to import it first, then use it:

```rust
// src/lib.rs
use wasm_bindgen::prelude::*;

// Import `alert` from the external JS
#[wasm_bindgen]
extern {
    pub fn alert(s: &str);
        // ^^^^^^^^^^^^^^ `alert` must be the same as JS fn name
        //                 and parameter name can be different
}

#[wasm_bindgen]
pub fn greet(name: &str) {
    alert(name)  // then we can use `alert` in Rust code
}
```
```bash
~/D/g/s/w/snake_game > wasm-pack build --target web
~/D/g/s/w/snake_game > cd www
~/D/g/s/w/s/www > npm run dev

# we can see the alert box with the message when we open the browser

```
