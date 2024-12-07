---
date: 2024-11-07
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Mutating instance's field value also need to add mut keyword

```rust
use rand::seq::SliceRandom;
use rand::thread_rng;

#[derive(Debug)]
struct Deck {
    cards: Vec<String>,
}

impl Deck {
    fn new() -> Self {
        let suits = ["Hearts", "Spades", "Clubs", "Diamonds"];
        let values = [
            "Ace", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine", "Ten", "Jack",
            "Queen", "King",
        ];

        let mut cards = vec![];

        for suit in suits {
            for value in values {
                let card = format!("{} of {}", value, suit);
                cards.push(card);
            }
        }

        Deck { cards }
    }

    fn shuffle(&self) {   // Error: self = Deck instance itself, so we need to add mut keyword to make it mutable
        let rng = thread_rng();   // Error: thread_rng() returns a random number generator, which need to be changed as time goes on, so we need to make it mutable
        self.cards.shuffle(&rng);  // Error: rng should be mutable, so we need to add mut keyword
    }
}

fn main() {
    let deck = Deck::new();  // Error: we need to shuffle the deck, so the instance should be mutable
    deck.shuffle();

    println!("Here your deck: {:#?}", deck.cards)
}
```

```rust
use rand::seq::SliceRandom;
use rand::thread_rng;

#[derive(Debug)]
struct Deck {
    cards: Vec<String>,
}

impl Deck {
    fn new() -> Self {
        let suits = ["Hearts", "Spades", "Clubs", "Diamonds"];
        let values = [
            "Ace", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine", "Ten", "Jack",
            "Queen", "King",
        ];

        let mut cards = vec![];

        for suit in suits {
            for value in values {
                let card = format!("{} of {}", value, suit);
                cards.push(card);
            }
        }

        Deck { cards }
    }

    fn shuffle(&mut self) {
    //         ^^^^ parameter should be mutable
        let mut rng = thread_rng();
        //  ^^^ random number generator should be mutable
        self.cards.shuffle(&mut rng);
        //                 ^^^^ random number generator should be mutable
    }
}

fn main() {
    let mut deck = Deck::new();
  //    ^^^ instance should be mutable
    deck.shuffle();

    println!("Here your deck: {:#?}", deck.cards)
}
```

So if a field in an instance needs to be changed, then the instance itself and the `self`` parameter referenced by the method must be marked with the `mut` keyword.

