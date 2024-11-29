---
date: 2024-11-05
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Define attribute of struct

```rust

#[derive(Debug)]  // define `derive` attribute of struct
//       ^^^^^ `derive` attribute can specify a trait to implement for the struct
struct Deck {
    cards: Vec<String>,
}

```

`Trait`` is a set of functions that a struct can implement. `derive` attribute can automatically implement some traits for the struct.

So `#[derive(Debug)]` on top of `Deck` struct will implement `Debug` trait to the struct.

In above case, it make `Deck` struct printable with `{:?}` [[2024-11-05_Formatter|formatter]].
