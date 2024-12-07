---
date: 2024-11-18
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# corner cases of into_iter method

In [[2024-11-17_Three-Iterator-pointer-type|this topic]], we know that there are three methods to create different type of iterators. And the third method is `into_iter` method. But there are some corner cases of `into_iter` method that we need to know.

`.into_iter()` will give you something different depending on how its called as below:

**1. Calling `into_iter` on a read-only reference**

```rust
    &colors  // read-only reference
        .into_iter()
        .for_each(|color| println!("{}", color));
        //         ^^^^^ got a read-only reference to each element

```

**2. Calling `into_iter` on a mutable reference**

```rust
    &mut colors  // mutable reference
        .into_iter()
        .for_each(|color| println!("{}", color));
        //         ^^^^^ got a mutable reference to each element

```

**3. Calling `into_iter` on a value**

```rust
    colors  // value
        .into_iter()
        .for_each(|color| println!("{}", color));
        //         ^^^^^ got value and ownership of each element

```

So in fact, we can only use `.into_iter()` to replace `iter()` or `mut_iter()`, but this may also cause some confusion or reduce the readability of the code, so it is recommended to only call `.into_iter()` after the value, not use these corner cases.
