---
date: 2024-11-13
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# How to handle error


## Requirement

```rust
fn divide(a: f64, b: f64) -> _ {

    if b == 0.0 {
        // Un oh, division by zero...
        // Even it is totally fine in Rust
        // We still want to treat it as an error here for demonstration
    }

    a / b
}

fn main() {
    println!("{}", divide(10.0, 0.0));
}

```


## Two options of handling error

There is two options to handle error in Rust:

1. **Panic**

Is the error so bad that we couldn't possibly keep running our program, or is it a completely unexpected error??

2. **Use the `Result` enum**

Is this an operation that can either succeed or fail.


>Usually, Panic is automatically triggered by the app. It is less common for us to manually write `panic` in the code. Typically, when we need to write error handling codes, we use option 2, which is to use the `Result` enum.


## `Result` enum

```rust
enum Result<T, E> {
//         ^^^^^^ T, E are generic, which is similar to arguments in function but for types
    OK(T),
    // ^
    Err(E)
    //  ^  types we offer to above <T, E> will be used inside { }, just like function arguments
}
```

## Real example

The following link provides a complete example of how to use the enum `Result` to handle errors in the divide function:

[[2024-11-13_Example-to-implement-error-handling-on-divide-function|Example to implement error handling on divide function]]
