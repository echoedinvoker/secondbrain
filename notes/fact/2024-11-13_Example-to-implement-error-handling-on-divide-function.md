---
date: 2024-11-13
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Example to implement error handling on divide function

```rust
use std::io::Error;  // Error is a built-in struct(type) in Rust to represent errors

fn divide(a: f64, b: f64) -> Result<f64, Error> {
//                        ^^^^^^^^^^^^^^^^^^^^^
    if b == 0.0 {
        Err(Error::other("Division by zero"))
    //  ^^^ Err is variant of Result enum, and Error is struct under io module, don't confuse between ._.
    } else {
        Ok(a / b)
    //  ^^ Ok is another variant of Result enum
    }
}

fn main() {

    // because divide function returns Result<f64, Error>, we need to handle all possible cases of Result
    match divide(5.0, 0.0) {
        Ok(result) => println!("Result: {}", result),
        Err(err) => println!("Error: {}", err),
    }
}

```

```bash
~/D/g/s/r/logs > cargo run -q
Error: Division by zero
```
