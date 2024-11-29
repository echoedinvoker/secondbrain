---
date: 2024-11-06
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Group strings by format macro

```rust
let suit = "Hearts";
let value = "Ace";

let card = format!("{} of {}", value, suit);

```

Or we can put binding into format placeholders directly:

```rust
let card = format!("{value} of {suit}");

```
