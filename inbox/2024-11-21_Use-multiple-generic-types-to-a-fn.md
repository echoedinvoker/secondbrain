---
date: 2024-11-21
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Use multiple generic types to a fn

We use the example from [[2024-11-20_Using-generic-to-make-fn-more-flexible|this topic]] as below:

```rust
fn solve<T: Float>(a: T, b: T) -> f64 {
//      ^^^^^^^^^^ only ONE generic type

    let a_f64 = a.to_f64().unwrap();
    let b_f64 = b.to_f64().unwrap();

    (a_f64.powf(2.0) + b_f64.powf(2.0)).sqrt()
}

fn main() {
    let a: f32 = 3.0; // two inputs
    let b: f32 = 4.0; // must be the same type

    println!("{}", solve(a, b));
}

```

If we want the function `solve` to accept different types, we can use multiple generic types:

```rust
fn solve<T: Float, U: Float>(a: T, b: U) -> f64 {
//                 ^^^^^^^^           ^^ add another generic type and use it

    let a_f64 = a.to_f64().unwrap();
    let b_f64 = b.to_f64().unwrap();

    (a_f64.powf(2.0) + b_f64.powf(2.0)).sqrt()
}

fn main() {

    let a: f32 = 3.0; // then two inputs' types
    let b: f64 = 4.0; // can be different

    println!("{}", solve(a, b));
}
```
