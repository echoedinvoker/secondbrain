---
date: 2024-11-08
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Example of rule 1 and 2 of twelve rules

From [[2024-11-07_Twelve-rules-of-writing-Rust-script|twelve rules of writing Rust script]]:

1. Every value is `owned` by a single variable, struct, vector, etc at a time

2. Reassigning the value to another variable, passing it to a function, putting it into a vector, etc, `moves` the value. The old variable can't be used anymore!


```rust
let deck = Deck::new();  // create a value Deck instance and bind it to binding `deck`
                         // then we can say that `deck` owns the value of the Deck instance
                         // or said that binding `deck` is the owner of the value of the Deck instance

```

```rust
let deck = Deck::new(); // There is no value binded to binding `deck` anymore

let other_deck = deck;  // move the value of the Deck instance from `deck` to `other_deck`
                        // the owner of the value of the Deck instance is now `other_deck`

println!("{:?}", deck);  // this will raise an error because there is no value binded to binding `deck`
```

