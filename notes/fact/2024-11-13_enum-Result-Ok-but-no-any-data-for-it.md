---
date: 2024-11-13
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# enum Result Ok but no any data for it

In [[2024-11-13_Example-to-implement-error-handling-on-divide-function|this]] case, `Ok` is tied to some data when function is successful. But what if function is successful but there is no data to return?

Below is the example of such case:

```rust
use std::io::Error;

fn verify_email(email: String) -> Result {
    if email.contains("@") {
        Ok()
    //  ^^^^ panic! `Ok` requires a value
    } else {
        Err(Error::other("Invalid email"))
    }
}

fn main() {
}

```

Conventionally, we use an empty [[2024-11-13_Tuple-in-Rust|tuple]] `()` to represent a scenario where the operation is successful but no data needs to be returned.

```rust
use std::io::Error;

fn verify_email(email: String) -> Result<(), Error> {
//                                      ^^^^ conventionally, empty tuple is used
//                                           to represent no data
    
    if email.contains("@") {
        Ok(())
    //     ^^ empty tuple
    } else {
        Err(Error::other("Invalid email"))
    }
}

fn main() {
    match verify_email(String::from("matt@mail.com")) {
        Ok(..) => println!("Email is valid"),
    //     ^^ use `..` to indicate that we got a data but we are not interested in it
        Err(e) => println!("Error: {}", e),
    }
}
```
