---
date: 2024-11-13
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Error types in Rust


## Example of using `std::io::Error` to create an error instance

```rust
use std::io::Error;  // Error is a struct to represent errors

fn divide(a: i32, b: i32) -> Result<i32, Error> {
  if b == 0 {
    Err(Error::other("Cannot divide by zero"));
    //  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    //  It is a bit like `Bank::new()`, they are all to create a new instance of a struct
    //  But Error don't use `new` as the name of static method for some reason
      
  } else {
    Ok(a / b)
  }
}

```

## There isn't really a general-purpose error type in Rust

In Python, we can use `Exception` as a general-purpose error type. And in javascript, we can use `Error` as a general-purpose error type. But in Rust, there isn't really a general-purpose error type.

```rust
use std::str::Utf8Error;

use std::string::FromUtf8Error;

use std::num::ParseIntError;
use std::num::ParseFloatError;
use std::num::TryFromIntError;

use std::thread::JoinError;

use std::io::Error;
```

From the above codes, it can be seen that Rust has many different error types, and these error types under different modules actually represent that they are designed for different situations, and there is no general-purpose error type.

## Custom error type

```rust
use std::io::Error;
//       ^^ this Error is under std::io module, which means it is designed for IO errors

fn divide(a: i32, b: i32) -> Result<i32, Error> {
  if b == 0 {
    Err(Error::other("Cannot divide by zero"));
    //  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ So in fact, using Error here is NOT a good practice
    //                                        maybe we should create a custom error here  
  } else {
    Ok(a / b)
  }
}

```

We will talk about how to create a custom error type in Rust later...
