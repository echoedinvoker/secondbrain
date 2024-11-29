---
date: 2024-11-15
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Function main can return Result

Usually the main function in Rust does not return any value, but we can make it return a Result as follows:

```rust
use std::io::Error;

fn main() -> Result<(), Error> {
//        ^^^^^^^^^^^^^^^^^^^^ we can define the return type of main function as Result<(), Error>


    // some codes which may produce an early return Err(Error)


    Ok(()) // because we defined the return type of main as Result<(), Error>, we should return Ok(()) at the end
}

```
The purpose of doing this is usually to work with the [[2024-11-15_Try-operation|try operation]] to handle any errors that may occur in the main function.
