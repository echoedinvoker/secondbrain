---
date: 2024-11-20
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# What is trait bound?

Trait can not only be used to add methods to struct like [[2024-11-20_What-is-trait?|this topic]], enum or primitives, but also to restrict the generic type of fn or method.

We use [[2024-11-20_Using-generic-to-make-fn-more-flexible|this example]] to explain it.

```rust
use num_traits::Float; // this line is to impl Float to some primitives (f32, f64, etc.) automatically

fn solve<T: Float>(a: T, b: T) -> f64 {
//        ^^^^^^^ this is a trait bound, which restricts the actual type to `T`
//                must implement the `Float` trait

    let a_f64 = a.to_f64().unwrap();
    let b_f64 = b.to_f64().unwrap();

    (a_f64.powf(2.0) + b_f64.powf(2.0)).sqrt()
}

fn main() {
    let a: f32 = 3.0;
    let b: f32 = 4.0;
    //     ^^^ this type already implements the `Float` trait
    //         so we can use it as the type of `T`

    println!("{}", solve(a, b));
}
```

```rust
