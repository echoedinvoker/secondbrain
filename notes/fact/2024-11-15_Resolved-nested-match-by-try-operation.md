---
date: 2024-11-15
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Resolved nested match by try operation


## Requirements: resovle nested matches

Below example comes from [[2024-11-15_Write-error-lines-to-txt-file|this topic]]

```rust
use std::fs;

 fn extract_errors(text: &str) -> Vec<&str> { ... }

fn main() {
    match fs::read_to_string("logs.txt") {
        Ok(text) => {
            let error_logs = extract_errors(&text);

            match fs::write("errors.txt", error_logs.join("\n")) {
                Ok(..) => println!("Wrote errors to errors.txt"),
                Err(error) => {
                    println!("Error: {}", error);
                }
            }
        }
        Err(error) => {
            println!("Error: {}", error);
        }
    }
}

```

Because `fs::read_to_string` and `fs::write` both return `Result` type, it produce nested matches codes structure which is not good for readability.


## Resolved by `?` operator

We can resovle nested matches issue by [[2024-11-15_Try-operation|try operation]] as below:

```rust
use std::io::Error; // `?` will return `Error` type if error occurs
use std::fs;

 fn extract_errors(text: &str) -> Vec<&str> { ... }

fn main() -> Result<(), Error> {
//       ^^^^^^^^^^^^^^^^^^^^^ `?` has chance to return `Error` type
//                             so we should define return type of main function as `Result`

    let text = fs::read_to_string("logs.txt")?;
    //                                       ^ when `Ok`, it will simply unwrap the value
    //                                         when `Err`, it will return `Error` type early
    fs::write("errors.txt", extract_errors(&text).join("\n"))?;
    //                                                       ^ same here

    Ok(())
//  ^^^^^^ because return type of main function is `Result`, we should return `Ok` here
}

```

