---
date: 2024-11-15
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Bug example about text data lifetime


## Codes of the bug example

Based on [[2024-11-14_Design-a-app-to-extract-log-and-print-ERROR-lines-only|this example]], we slightly modify it to the following codes:

```rust
use std::fs;

fn extract_errors(text: &str) -> Vec<&str> {
    let split_text = text.split("\n");

    let mut errors = vec![];

    for line in split_text {
        if line.starts_with("ERROR") {
            errors.push(line);
        }
    }

    errors
}

fn main() {
    let mut error_logs = vec![];
//  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ add this variable to store the extracted errors text later

    match fs::read_to_string("logs.txt") {
        Ok(text) => {
            error_logs = extract_errors(&text);
        //  ^^^^^^^^^^^^ instead of printing the errors directly, we store it to the variable
        //               `error_logs` on the main scope and print it later
        }
        Err(error) => {
            println!("Error: {}", error);
        }
    }

    println!("{:#?}", error_logs);
//  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ print the error logs
}

```

Originally, the error lines were directly printed in the scope of `Ok`. It has been changed to first use the main scope variable `error_logs` to store the error lines, and print them all together at the end.

It looks like just a very ordinary change, but the &text in extract_errors(&text) will generate error messages as follows:
```shell
Diagnostics:
1. `text` does not live long enough
   borrowed value does not live long enough [E0597]
```


## Reason of the bug

The reason for the bug is that the owner of the text data has always been `Ok`, but the variable `error_logs` is outside the `Ok` scope, so an error occurred.

```rust
fn extract_errors(text: &str) -> Vec<&str> {
//                ^^^^^^^^^^^^^^^^^^^^^^^^ input and output are all references
//                                         never move ownership of text data
}

fn main() {
    let mut error_logs = vec![];

    match fs::read_to_string("logs.txt") {
        Ok(text) => {
    //  ^^^^^^^^ text data ownership is moved to Ok scope
            error_logs = extract_errors(&text);
        //  ^^^^^^^^^^ variable on main scope store the reference of text data

        //  end of the Ok scope, text data is dropped here
        //  but `error_logs` still holds the reference of text data, so the error occurs
        }
        // ...
    }
    // ...
}

```

## Fix the bug

```rust
fn extract_errors(text: &str) -> Vec<String> {
//                                   ^^^^^^ instead of &str, change to String type
    let split_text = text.split("\n");

    let mut errors = vec![];

    for line in split_text {
        if line.starts_with("ERROR") {
            errors.push(line.to_string());
            //              ^^^^^^^^^^^^ convert &str to String
            //                           this step copies the text data value,
            //                           create new ownership in the heap
        }
    }

    errors // instead of &str, return Vec<String> now, it moves the ownership to outside
}

fn main() {
    let mut error_logs = vec![];

    match fs::read_to_string("logs.txt") {
        Ok(text) => {
            error_logs = extract_errors(&text);
        //  ^^^^^^^^^^ got ownershipe of text data, which is different from the original text data


        // end of the Ok scope, original text data is dropped here
        // but `error_logs` still holds the ownership of new copies text data, so no error occurs
        }
        Err(error) => {
            println!("Error: {}", error);
        }
    }

    println!("{:#?}", error_logs);
}

```


## Discussion

Can we establish a rule to prevent the same mistake from happening in the future?

The answer is no, even though we have made corrections to the codes above to remove errors, the downside of this fix is still very obvious, which is the need to copy text data in the heap, increasing memory usage. If the text data is large, this approach is not very suitable.

Therefore, the correct approach is to choose the appropriate processing method based on the lifecycle required by the text data. If the above example does not require specifying to print error logs in the main scope, then there is no need to generate new ownership, so the original &str can be used to process text data and occupy less memory space.
