---
date: 2024-11-12
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Handle enum Option value by methods

In addition to `match` and `if let`, we can handle values through methods of Option. Here are three methods introduced below.

These methods are much more compact compared to `match` and `if let`, but they often have significant downsides (which crash your app easily). We typically only use them in debugging, blog posts, or documentation, and do not recommend using them in production code.


## .unwrap()

Simply unwraps `Some` value and return it. But if the value is `None`, it will panic.

```rust
fn main() {
    let values = vec![1, 2, 3];

    println!("{}", values.get(0).unwrap()); // 1
  //               ^^^^^^^^^^^^^ Some(1)
    println!("{}", values.get(3).unwrap()); // panic! app will crash immediately
}
```

## .expect()

Basically the same as `unwrap()`, but you can customize the panic message.

```rust
fn main() {
    let values = vec![1, 2, 3];
    println!("{}", values.get(0).expect("Index out of range")); // 1
  //               ^^^^^^^^^^^^^ Some(1)
    println!("{}", values.get(3).expect("Index out of range")); // panic! app will crash immediately with a message 'Index out of range'
}
```

## .unwrap_or()

Basically the same as `unwrap()`, but you can provide a default value when the value is `None`.

```rust
fn main() {
    let values = vec![1, 2, 3];
    println!("{}", values.get(0).unwrap_or(0)); // 1
  //               ^^^^^^^^^^^^^ Some(1)
    println!("{}", values.get(3).unwrap_or(0)); // 0, no panic
  //               ^^^^^^^^^^^^^ None           // this is useful to prevent app crash when debugging
}
```

We can find more methods of the enum `Option` in the official documentation: [Option](https://doc.rust-lang.org/std/option/enum.Option.html)
