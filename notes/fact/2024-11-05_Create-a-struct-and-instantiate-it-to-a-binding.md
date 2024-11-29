---
date: 2024-11-05
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create a struct and instantiate it to a binding

```rust

struct Deck {  // struct is define some data and methods, such as a class in other languages

    cards: Vec<String>,
//  ^^^^^^^^^^^^^^^^^^ here we define a field named cards of type vector of strings
}

fn main() {
    let deck = Deck { cards: vec![] };
//  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ here we instantiate Deck to a binding named deck with default value
}

```


