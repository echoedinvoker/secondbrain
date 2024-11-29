---
date: 2024-11-07
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Make all vector has shuffle method

```rust
use rand::seq::SliceRandom; // a trait that provides the shuffle method to all vectors automatically
use rand::thread_rng; // to generate random numbers generator

fn main() {
  let mut cards = vec![1, 2, 3, 4, 5];

  let mut rng = thread_rng(); // create a random number generator for .shuffle() bellow
  self.cards.shuffle(&mut rng);
  //        ^^^^^^^^ as long as we use the trait `SliceRandom`, we can call the shuffle method on any vector
}
```

