---
date: 2024-11-14
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Design a app to extract log and print ERROR lines only

Below is the complete code with special comments on [[2024-11-14_Multiply-String-types-in-Rust|string types]] and ownership, which is matter of this case. 

We also used the [[2024-11-13_Read-txt-file|fs module]] to read the content of a txt file.

```rust
use std::fs;

fn extract_errors(text: &str) -> Vec<&str> {
//                      ^^^^ only if we want to store string we use String, otherwise we use &str
    
    let split_text = text.split("\n");
    //               ^^^^ text is read-only reference, so if we re-assign it, it copies the value
    //                    if we use its method, it returns a new value or reference
    //                    there is no any ownership transfer

    let mut errors = vec![];
    //  ^^^ we will store &str of multiple error lines to it, so it must be mutable

    for line in split_text {
    //  ^^^^ &str also
        if line.starts_with("ERROR") {
        //     ^^^^^^^^^^^^^^^^^^^^^ return boolean value, no ownership
        
            errors.push(line);
            //    ^^^^^^^^^^^ line is &str, so it is copied to the vector
        }
    }

    errors
//  ^^^^^^ return the vector of multiple &str, but who owns these &str?
//         original &str value comes from argument `text`, so the ownership is outside of this function
}

fn main() {
    match fs::read_to_string("logs.txt") {
        Ok(text) => {
    //     ^^^^ String value, this is the ownership of the text which we pass to the function `extract_errors`

            let errors = extract_errors(&text);
            //  ^^^^^^ get Vec<&str> value from the function `extract_errors` return
            for error in errors {
            //  ^^^^^ &str from  Vec<&str>
                println!("{}", error);
                //             ^^^^^ use &str value
            }

        //   end of the scope, `text` is dropped here
        //   we didn't return any &str value out of this scope, so no error happens :D
        }
        Err(error) => {
            println!("Error: {}", error);
        }
    }
}

```

## Variant of above code

We made a slight change to the above codes and generated a bug in the following link. This bug will help us deepen our understanding of ownership, string types, and memory allocation concepts.

[[2024-11-15_Bug-example-about-text-data-lifetime|Bug example about text data lifetime]]


## Further adding a feature to write error lines to a file

[[2024-11-15_Write-error-lines-to-txt-file|write error lines to txt file]]

