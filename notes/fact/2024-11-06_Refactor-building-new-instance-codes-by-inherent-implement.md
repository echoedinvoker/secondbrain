---
date: 2024-11-06
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Refactor building new instance codes by inherent implement

```rust
#[derive(Debug)]
struct Deck {
    cards: Vec<String>,
}

fn main() {

    // start to build a deck instance
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

    let deck = Deck { cards };
    // end to build a deck instance

    println!("Here your deck: {:#?}", deck.cards)
}

```

In the code above, the block of code inside main is actually creating an instance of Deck. This process can be extracted out and made more intuitive through implementing [[2024-11-06_Inherent-implement|inherent implement]]].

```rust
#[derive(Debug)]
struct Deck {
    cards: Vec<String>,
}

impl Deck {
//   ^^^^ It must be the name of the struct or enum that we are implementing, that's why we call it `inherent` impl

    fn new() -> Self {
    //       ^^^^^^^ In Rust, if a fn has return value, we need to declare the type of return value explicitly
    //               `Self` is a special keyword in Rust, it refers to the type of the struct or enum that we are implementing

        // move building codes from main to fn new of inherent impl
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
    //  ^^^^^^^^^^^^^^ in Rust, last line of { } block is returned, we don't need to use return keyword, just write the value
    }
}

fn main() {
    let deck = Deck::new();
//             ^^^^^^^^^^^^ struct name and `::` to use associated function

    println!("Here your deck: {:#?}", deck.cards)
}

```
