---
date: 2024-11-20
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Convert number type manually

From [[2024-11-20_Algorithm-operation-only-works-on-same-type-values|this topic]], we know that Rust doesn't automatically convert between number types. However, we can convert them manually by several ways:

**1. Using `as` keyword**

```rust
fn main() {
    let a: f32 = 1.0;
    let b = 2.0 as f32; // Convert type from f64 to f32

    let c = a + b; // Works
}
```

**2. Using `num_traits` crate**

```bash
cargo add num-traits
```

```rust
use num_traits::ToPrimitive;  // it adds some number conversion methods to all number types values

fn main() {
    let a: f32 = 1.0;
    let b = 2.0; // f64
    
    let b_f32 = b.to_f32().unwrap();
    //           ^^^^^^^^^ from num_traits crate, it convert f64 to f32 but return Option<f32>
    //                     unwrap() is used to get the value from Option

    let c = a + b_f32; // Works
}
```
