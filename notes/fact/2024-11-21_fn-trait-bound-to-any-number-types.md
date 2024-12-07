---
date: 2024-11-21
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# fn trait bound to any number types

We use the example from [[2024-11-21_Use-multiple-generic-types-to-a-fn|this topic]] as below:

```rust
use num_traits::Float;

fn solve<T: Float, U: Float>(a: T, b: U) -> f64 {
//        ^^^^^^^   ^^^^^^^ trait bound to limit input types must be float (e.g. f32, f64)
    let a_f64 = a.to_f64().unwrap();
    let b_f64 = b.to_f64().unwrap();

    (a_f64.powf(2.0) + b_f64.powf(2.0)).sqrt()
}

fn main() {
    let a: f32 = 3.0;
    //     ^^^
    let b: f64 = 4.0;
    //     ^^^ 

    println!("{}", solve(a, b));
}
```

If we want to accept any number type, not limited to floating point types, we can replace the `Float` trait with the `ToPrimitive` trait as follows:

```rust
use num_traits::ToPrimitive;
//              ^^^^^^^^^^^ replace Float with ToPrimitive trait, which is implemented for all number types in num_traits crate

fn solve<T: ToPrimitive, U: ToPrimitive>(a: T, b: U) -> f64 {
//          ^^^^^^^^^^^     ^^^^^^^^^^^
    let a_f64 = a.to_f64().unwrap();
    let b_f64 = b.to_f64().unwrap();

    (a_f64.powf(2.0) + b_f64.powf(2.0)).sqrt()
}

fn main() {
    let a: i32 = 3;
    //     ^^^ integer type, not floating point
    let b: u8 = 4;
    //     ^^  unsigned integer type
    println!("{}", solve(a, b));
}
```

The difference between Float and ToPrimitive traits is that the Float trait is only implemented for all floating-point types in the crate `num_traits`, while the ToPrimitive trait is implemented for all numeric types. This way, we can accept input parameters of any numeric type.

> When using a trait as a trait bound, the most important thing is not which methods are inside the trait, but rather on which struct/enum/primitive type the trait is implemented.
