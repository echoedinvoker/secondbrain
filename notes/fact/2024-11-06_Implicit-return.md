---
date: 2024-11-06
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Implicit return

```rust
fn is_even(n: i32) -> bool {
    return n % 2 == 0;
}

```
The above is a general way of writing a function, it uses the `return` keyword to return a value. However, in Rust, if the last thing executed in a function is an expression, that expression will be treated as the return value. So the above function can be written as:

```rust
fn is_even(n: i32) -> bool {
    n % 2 == 0
//  ^^^^^^^^^^ this is the return value
}

```

>Special attention must be paid to removing the ending ;, otherwise it will become a statement instead of an expression.


There is another example:

```rust
fn is_even(n: i32) -> bool {
    if n % 2 == 0 {
        true
    //  ^^^^ this is also the return value, because it is the last expression
    //       to be executed in the function when the condition is true
    } else {
        false
    }
}

```
So the main thing to look at is whether it is **executed last**, not whether it is the last line in position.
