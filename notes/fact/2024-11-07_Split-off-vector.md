---
date: 2024-11-07
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Split off vector

Rust standard library provides a method called `split_off` for `Vec`. It can remove a range of items from the original vector and move them to another vector.

You can check out the [rust std webside](https://doc.rust-lang.org/std/), and search std::vec::Vec::split_off. Then you can find the document about the split_off method of Vec.

Bellow is the example code:

```rust
let mut v = vec!['a', 'b', 'c', 'd', 'e', 'f'];
//                         ^^^^^^^^^^^^^^^^^^ assume we want to remove items from index 2
//                                            and move them to another vector.

let v2 = v.split_off(2);

println!("{:?}", v);  // ['a', 'b']
//                       So we can said it equals to keep first 2 items of original vector.
println!("{:?}", v2); // ['c', 'd', 'e', 'f']

```

Instead of keeping the first 2 items, if we want to remove last 2 items, we can use the following code:

```rust
let mut v = vec!['a', 'b', 'c', 'd', 'e', 'f'];
//                                   ^^^^^^^^ we want to remove last 2 items to another vector.

let v2 = v.split_off(v.len() - 2);
//                   ^^^^^^^^^^^ 6 - 2 = 4, it will remove items from index 4 to the end.
//                               which equals to remove last 2 items.

println!("{:?}", v);  // ['a', 'b', 'c', 'd']
println!("{:?}", v2); // ['e', 'f']

```

[[2024-11-07_Example-to-use-vector-split-off|here]] is a real world example to use `split_off` method. 
