---
date: 2024-11-06
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Print list data with better formatter

This is a real case of [[2024-11-05_Formatter|formatter]] to print list data in Rust. 

```rust
println!("Here your deck: {:?}", deck)  // assume deck is a Vec<Srting> which is a list of cards

```
Then we got this output:

```bash
Here your deck: Deck { cards: ["Ace of Hearts", "Two of Hearts", "Three of Hearts", "Four of Heart
s", "Five of Hearts", "Six of Hearts", "Seven of Hearts", "Eight of Hearts", "Nine of Hearts", "Te
n of Hearts", "Jack of Hearts", "Queen of Hearts", "King of Hearts", "Ace of Spades", "Two of Spad
es", "Three of Spades", "Four of Spades", "Five of Spades", "Six of Spades", "Seven of Spades", "E
ight of Spades", "Nine of Spades", "Ten of Spades", "Jack of Spades", "Queen of Spades", "King of 
Spades", "Ace of Clubs", "Two of Clubs", "Three of Clubs", "Four of Clubs", "Five of Clubs", "Six 
of Clubs", "Seven of Clubs", "Eight of Clubs", "Nine of Clubs", "Ten of Clubs", "Jack of Clubs", "
Queen of Clubs", "King of Clubs", "Ace of Diamonds", "Two of Diamonds", "Three of Diamonds", "Four
 of Diamonds", "Five of Diamonds", "Six of Diamonds", "Seven of Diamonds", "Eight of Diamonds", "N
ine of Diamonds", "Ten of Diamonds", "Jack of Diamonds", "Queen of Diamonds", "King of Diamonds"] 
}
```

We can change formatter from `:?` to `:#?` to get a better output:

```rust
println!("Here your deck: {:#?}", deck)
```
Then we got this output:

```bash
Here your deck: Deck {
    cards: [
        "Ace of Hearts",
        "Two of Hearts",
        "Three of Hearts",
        "Four of Hearts",
        "Five of Hearts",
        "Six of Hearts",
        "Seven of Hearts",
        "Eight of Hearts",
        "Nine of Hearts",
        "Ten of Hearts",
        "Jack of Hearts",
        "Queen of Hearts",
        "King of Hearts",
        "Ace of Spades",
        "Two of Spades",
        "Three of Spades",
        "Four of Spades",
        "Five of Spades",
        "Six of Spades",
        "Seven of Spades",
        "Eight of Spades",
        "Nine of Spades",
        "Ten of Spades",
        "Jack of Spades",
        "Queen of Spades",
        "King of Spades",
        "Ace of Clubs",
        "Two of Clubs",
        "Three of Clubs",
        "Four of Clubs",
        "Five of Clubs",
        "Six of Clubs",
        "Seven of Clubs",
        "Eight of Clubs",
        "Nine of Clubs",
        "Ten of Clubs",
        "Jack of Clubs",
        "Queen of Clubs",
        "King of Clubs",
        "Ace of Diamonds",
        "Two of Diamonds",
        "Three of Diamonds",
        "Four of Diamonds",
        "Five of Diamonds",
        "Six of Diamonds",
        "Seven of Diamonds",
        "Eight of Diamonds",
        "Nine of Diamonds",
        "Ten of Diamonds",
        "Jack of Diamonds",
        "Queen of Diamonds",
        "King of Diamonds",
    ],
}
```
