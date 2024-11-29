---
date: 2024-11-07
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Example to use vector split off

```rust
struct Deck {
    cards: Vec<String>,
}

impl Deck {
    fn new() -> Self { ... }

    fn shuffle(&mut self) { ... }

    fn deal(&mut self, num_cards: usize) -> Vec<String> {
    //      ^^^^                  ^^^^^ for indicating size, usually `usize`` is used
    //      because split_off mutates the vector
        self.cards.split_off(self.cards.len() - num_cards)
        //        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        //        std::vec::Vec::split_off is build-in function
        //        so we can directly use it on vectors
    }
}

fn main() {
    let mut deck = Deck::new();
    deck.shuffle();

    let hand = deck.deal(5);
    //             ^^^^^^^^^

    println!("{:?}", hand);
}

```

