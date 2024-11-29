---
date: 2024-11-16
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Iter value in Rust is LAZY

It means when we create an Iter value:

```rust
let colors = vec!["red", "green", "blue"];

let iter = colors.iter();  // create an Iter value

```
Although the Iter value has been generated, the iteration will not be executed immediately.

Iteration will only occur in the following two scenarios:

1. call `.next()` method manually

2. call [[2024-11-16_Iter-consumer|consumer method]] like `.for_each()`, `.collect()`, `.sum()`, which use `.next()` method internally


That's why we call iteration in Rust is **LAZY**.
