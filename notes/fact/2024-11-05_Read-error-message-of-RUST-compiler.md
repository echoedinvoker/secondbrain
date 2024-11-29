---
date: 2024-11-05
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Read error message of RUST compiler

```rust
struct Deck {
    cards: Vec<String>,
}

fn main() {
    let deck = Deck { cards: vec![] };

    println!("Here your deck: {}", deck)
//  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ `Deck` doesn't implement `std::fmt::Display`
//                                       the trait `std::fmt::Display` is not implemented for `Deck`
//                                       in format strings you may be able to use `{:?}` (or {:#?} for pretty-print) instead
}
```
It is recommended to read the error messages of the RUST compiler before using Google. In addition to the reasons for the error, it usually also provides clear solutions.

We have made the following modifications based on the last line of the error message above:

```rust
```rust
struct Deck {
    cards: Vec<String>,
}

fn main() {
    let deck = Deck { cards: vec![] };

    println!("Here your deck: {:?}", deck)
//  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ `Deck` doesn't implement `std::fmt::Debug`
//                                         the trait `std::fmt::Debug` is not implemented for `Deck`
//                                         add `#[derive(Debug)]` to `Deck` or manually `impl std::fmt::Debug for Deck`
}

```

Still having the error message but changed (need to save file to change error), following the recommendation of the error message:


```rust
#[derive(Debug)]
struct Deck {
    cards: Vec<String>,
}

fn main() {
    let deck = Deck { cards: vec![] };

    println!("Here your deck: {:?}", deck)
}

```

Now the code runs without errors and the output is:
```bash
~/D/g/s/r/deck > cargo run -q
warning: field `cards` is never read
 --> src/main.rs:3:5
  |
2 | struct Deck {
  |        ---- field in this struct
3 |     cards: Vec<String>,
  |     ^^^^^
  |
  = note: `Deck` has a derived impl for the trait `Debug`, but this is intentionally ignored durin
g dead code analysis
  = note: `#[warn(dead_code)]` on by default

Here your deck: Deck { cards: [] }

```

If you want to check the details of the error message, bellow are related links:
[[2024-11-05_Formatter|Formatter]]
[[2024-11-05_Define-attribute-of-struct|Define attribute of struct]]
