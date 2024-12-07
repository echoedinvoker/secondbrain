---
date: 2024-11-15
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Write error lines to txt file

Base on [[2024-11-14_Design-a-app-to-extract-log-and-print-ERROR-lines-only|this example]], we add a new feature to write error lines to a txt file as below:

```rust
use std::fs;

fn extract_errors(text: &str) -> Vec<&str> {
  //                                 ^^^^ we write txt file in the Ok scope,
  //                                      so we don't need to convert reference to ownership
  //                                      using reference is more efficient and occupies less memory
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
    match fs::read_to_string("logs.txt") {
        Ok(text) => {
            let error_logs = extract_errors(&text);

            // Write errors to a file, the method `write` comes from the `fs` module just like `read_to_string`
            match fs::write("errors.txt", error_logs.join("\n")) {
      //    ^^^^^                                   ^^^^^^^^^^^ when it work on &str, it will return &str as well
      //    because `write` returns a `Result` we should use `match` or `if let` to handle all possible outcomes
                Ok(..) => println!("Wrote errors to errors.txt"),
              //   ^^ it will get `()` here, and we don't interest in it, so we use `..` to ignore it
               
                Err(error) => {
                    println!("Error: {}", error);
                }
            }

            // Ok scope ends, so text data is dropped here
            // we write the error logs insides this scope, so there is no error happens here
            // but for handling the `fs::write` Result, the nested `match` is used, which is nasty to read
        }
        Err(error) => {
            println!("Error: {}", error);
        }
    }
}
```

Because `fs::write` returns a `Result`, we must use `match` or `if let` to handle all possible outcomes. Although no errors occur, the nested code structure makes the code difficult to read.

[[2024-11-15_Resovled-nested-match-codes-by-Result-debugging-methods|here]]  is the topic to resolve this issue bye debugging methods of `Result`. 

[[2024-11-15_Resolved-nested-match-by-try-operation|here]] is the topic to resolve this issue by using the `?` operator.

We have match, debugging methods, and try operator to handle Result type value. None of them is the best, the most suitable method must be chosen according to the situation.


## When to use which ways to handle Result value

[[2024-11-15_When-to-use-which-ways-to-handle-Result-value|here]] is the topic to compare when to use which way to handle the Result value by detailed examples.
 
But in this case, better to use try operator to do error handling, because we don't have anything else to do when Err occurs. 
