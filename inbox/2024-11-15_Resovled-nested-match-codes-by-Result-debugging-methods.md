---
date: 2024-11-15
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Resovled nested match codes by Result debugging methods


## Requirement: nested match codes

Codes below comes from [[2024-11-15_Write-error-lines-to-txt-file|this example]]:

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

Because `fs::read_to_string` and `fs::write` both return `Result` type, the above codes generate a nested match structure, which reduces the readability of the codes.


## Resolve it by debugging methods of Result

Similar to enum Option, enum Result has almost identical [[2024-11-12_Handle-enum-Option-value-by-methods|debugging methods]], which will help us simplify the nested match codes above.

```rust
use std::fs;

fn extract_errors(text: &str) -> Vec<&str> { ... }

fn main() {
    let text = fs::read_to_string("logs.txt").expect("Failed to read logs.txt");
  //    ^^^^ get String value                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ using debugging method to unwrap the Result value directly
    
    fs::write("errors.txt", extract_errors(&text).join("\n")).expect("Failed to write errors.txt");                     // because fs::write return empty tuple when succeed,
                                                          // ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ unwrap Result           there is no need to assign it to any variable
}

```

You can see that the codes are very concise, completely eliminating nested structure, but the debugging method will directly generate panic and crash the app when it fails, which greatly limits the means of error handling.
