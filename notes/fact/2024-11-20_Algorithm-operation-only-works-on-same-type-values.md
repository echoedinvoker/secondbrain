---
date: 2024-11-20
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Algorithm operation only works on same type values

```rust
fn main () {
  let a: f32 = 5.0;
  let b = 3.0;  // default is f64

  let c = a + b; // error: mismatched types, because a and b are different types
                 // even though they are both floating point numbers
}
```

```rust
fn main () {
  let a: f32 = 5.0;
  let b: i32 = 3.0;
  let c = a + b // error: mismatched types, because a and b are different types
}
```

From the examples above, we can see:

1. Mathematical operations can only be performed between values of the same type.
2. Rust does not automatically convert types to perform operations.

We can know how to convert number types manually in [[2024-11-20_Convert-number-type-manually|this topic]].
