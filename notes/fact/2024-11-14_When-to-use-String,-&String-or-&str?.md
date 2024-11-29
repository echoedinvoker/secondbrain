---
date: 2024-11-14
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# When to use String, &String or &str?

Here's the table converted to markdown format:

| Name | When to use | Uses memory in... | Notes |
|------|-------------|------------------|--------|
| String | When you want to take ownership of text data.<br>When you have a string that might grow or shrink. | Stack<br>and<br>Heap | |
| &String | Usually never | Stack | Rust automatically turns &String into a &str for you. |
| &str | When you want to read all or a portion of some text owned by something else. | Stack | Refers directly to heap-allocated or data-allocated text |

## Reason we need ownership of text data

Only with ownership can we store text data in, for example, a vector or struct field.


## Benefits of using &str

1. High performance
2. Less memory usage


## Prove that Rust automatically turns &String into &str

```rust
fn string_test(s: &str) {
//                ^^^^
    println!("{}", s);
}

fn main() {
  string_test(&String::from("Hello"));
  //          ^^^^^^^^^^^^^^^^^^^^^^ This value is type of `&String` but no any error happened
  //                                 because Rust change it to `&str` under the hood automatically
} 


