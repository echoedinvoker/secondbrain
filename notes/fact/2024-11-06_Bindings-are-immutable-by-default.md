---
date: 2024-11-06
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Bindings are immutable by default

That's why we call them `binding` instead of `variable`.

```rust

let cards = vec![];  // even we use `let` keyword, the binding is immutable by default

// Error: cannot change the value of an immutable binding
cards.push("Ace of Hearts");

// Error: canoot reassign an immutable binding
cards = vec!["Ace of Hearts"];

```

If you want to make a binding mutable, you can use the `mut` keyword:

```rust
let mut cards = vec![];
//  ^^^ make the binding mutable

cards.push("Ace of Hearts"); // Changes the value of the binding, OK

cards = vec!["Ace of Hearts"]; // Reassigns the binding, OK
```
