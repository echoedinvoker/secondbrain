---
date: 2024-11-07
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Use things insides modules


![rust-module.png](../assets/imgs/rust-module.png)

## Use external modules directly

```rust
fn main() {
  let rng = rand::thread_rng();
  //        ^^^^^^ crate name with `::` then use its function directly
  let slice = rand:seq::SliceRandom;
  //              ^^^^  if you want to use a module inside a crate
  //                    just continue with `:` and the module name
}

```

## Use internal modules

Not like external modules, internal modules are defined inside the same file. We can NOT use them directly. We need to use the `mod` keyword to import them.

```rust
mod games;  // import the internal submodule

fn main() {
  let deck = games::Deck::new();
  //         ^^^^^^^ still need to type the module name
}

```


