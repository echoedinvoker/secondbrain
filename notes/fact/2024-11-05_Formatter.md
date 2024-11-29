---
date: 2024-11-05
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Formatter


```rust
struct Deck {
    cards: Vec<String>,
}

fn main() {
    let deck = Deck { cards: vec![] };

    println!("Here your deck: {:?}", deck)
//                            ^^^^ this is formatter placeholder
//                                 and `:?` is formatter, which means to print debug info
}

```

There are many other formatters:

  - `{:?}`: debug
  - `{}`: display
  - `{:#?}`: pretty print
  - `{:.2}`: precision
  - `{:+}`: sign
  - `{:x}`: hexadecimal
  - `{:.2e}`: scientific notation
  - `{:.2?}`: debug with precision
  - `{:.2$}`: positional


