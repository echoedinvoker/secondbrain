---
date: 2024-11-05
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create empty vector data

We have two options to create an empty vector data in Rust:


1. by using the `Vec::new()` method

```rust
let mut v: Vec<i32> = Vec::new();
//                    ^^^^^^^^^^
```

2. by using the `vec![]` macro
```rust
let mut v: Vec<i32> = vec![];
//                    ^^^^^^^
```

Two options are 100% equivalent and you can use any of them. 

