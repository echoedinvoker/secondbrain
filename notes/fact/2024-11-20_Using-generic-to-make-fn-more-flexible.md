---
date: 2024-11-20
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Using generic to make fn more flexible

Assume we want to write a function `solve` to calculate the hypotenuse of a right triangle:

```rust
fn solve(a: f64, b: f64) -> f64 {
    (a.powf(2.0) + b.powf(2.0)).sqrt()
}

fn main() {
    println!("{}", solve(3.0, 4.0));
}
```

Because of [[2024-11-20_Algorithm-operation-only-works-on-same-type-values|this reason]], we often encounter situations where we need to manually convert the type of numbers passed into the function `solve` because they are not exactly the same type. (e.g. `f32` or `i64`)

So, we try to add codes to convert type in the function `solve`:

```rust
fn solve(a: f64, b: f64) -> f64 {

    // no matter what type of a and b are passed, we convert them to f64
    let a_f64 = a.to_f64().unwrap();
    let b_f64 = b.to_f64().unwrap();
    
    (a_f64.powf(2.0) + b_f64.powf(2.0)).sqrt()
//   ^^^^^             ^^^^^
}

fn main() {
    println!("{}", solve(3.0, 4.0));
}
```

But still have problem... we cannot pass numbers which type is not `f64` to the function `solve` because it only accepts `f64` type.

```rust
fn solve(a: f64, b: f64) -> f64 {
//          ^^^     ^^^  only accepts `f64` type, which make following type convertion meaningless...
```

Then, we can use generic to make the function `solve` accept types more flexible:

```rust
use num_traits::Float;  // add this line to use Float trait, we will talk about it later

fn solve<T: Float>(a: T, b: T) -> f64 {
//      ^^^^^^^^^^    ^     ^
//         ^          ^^^^^^^^^^ when Rust knows the actual type of T, these two `T` will be replaced by that type
//         ^                     Just like placeholder of argument
//         ^
//         generic type list, similar to argument list but type version

    let a_f64 = a.to_f64().unwrap();
    let b_f64 = b.to_f64().unwrap();

    (a_f64.powf(2.0) + b_f64.powf(2.0)).sqrt()
}

fn main() {
    let a: f32 = 3.0;  // not f64
    let b: f32 = 4.0;  // not f64

    println!("{}", solve::<f32>(a, b));
    //                  ^^^^^^^ just like pass value to argument list of a function
    //                          here we pass type to generic type list of a function
}

```

We can let Rust to infer the type of `T` by itself:

```rust
use num_traits::Float;

fn solve<T: Float>(a: T, b: T) -> f64 {
    let a_f64 = a.to_f64().unwrap();
    let b_f64 = b.to_f64().unwrap();

    (a_f64.powf(2.0) + b_f64.powf(2.0)).sqrt()
}

fn main() {
    let a: f32 = 3.0;
    let b: f32 = 4.0;

    println!("{}", solve(a, b)); // Rust can infer the type of T from the arguments=
                                 // so we don't need to specify the type of T with `::<f32>`
}
```
