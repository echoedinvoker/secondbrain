---
date: 2024-11-14
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Multiply String types in Rust

```rust
fn string_test(a: String, b: &String, c: &str) {
//                ^^^^^^     ^^^^^^^     ^^^^  We have three different types we can use when passing strings to a function
//                                             &String is just read-only reference of String
//                                             &str is so called `string slice`
    println!("a: {}", a);
    println!("b: {}", b);
    println!("c: {}", c);
}

fn main() {
    string_test(String::from("red"), &String::from("red"), "red");
    //          ^^^^^^^^^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^^^  ^^^^^
}

```

`String` and `&str` values can change their types to each other:

```rust
fn string_test(a: String, b: &String, c: &str) {
    println!("a: {}", a);
    println!("b: {}", b);
    println!("c: {}", c);
}

fn main() {
    string_test(
        "red".to_string(),
    //       ^^^^^^^^^^^^ change &str to String
        &String::from("red"),
        String::from("red").as_str(),
    //                     ^^^^^^^^^ change String to &str
    );
}
```
Their main difference lies in the method of [[2024-11-14_Memory-distributions-of-different-string-types|memory allocation way]].
