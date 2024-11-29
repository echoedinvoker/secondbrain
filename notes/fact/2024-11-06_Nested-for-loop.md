---
date: 2024-11-06
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Nested for loop

```rust
let suits = ["Hearts", "Spades", "Clubs", "Diamonds"];
let values = ["Ace", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine", "Ten", "Jack", "Queen", "King"];

for suit in suits {
    for value in values {
        println!("{} of {}", value, suit);
    }
}

```

