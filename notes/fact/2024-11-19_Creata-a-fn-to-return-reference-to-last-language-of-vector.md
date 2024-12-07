---
date: 2024-11-19
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Creata a fn to return reference to last language of vector

```rust
fn last_language(languages: &[String]) -> &str {
//                          ^^^^^^^^^     ^^^^
//                          argument and return are all references, do we need to add lifetimes annotation?

    match languages.last() {
    //             ^^^^^^^ `.last()` is a method of vector that returns an Option value
    //                     when there are elements in the vector, it returns `Some(last_element)`
    //                     when the vector is empty, it returns `None`
      
        Some(lang) => lang,
        None => "No languages found",
    }
}

fn main() {
    let languages = vec![
        String::from("Rust"),
        String::from("Python"),
        String::from("JavaScript"),
    ];

    println!("Last language: {}", last_language(&languages));
    //                            ^^^^^^^^^^^^^^^^^^^^^^^^^
}

```

In [[2024-11-19_Big-assumption-when-fn-args-and-return-type-are-all-reference|this topic]], it is mentioned that we need to add lifetimes annotation when the arguments and return type are all references.

However, this case meets the conditions of [[2024-11-19_Corner-cases-of-lifetime-annotation|lifetime annotation elision]], so there is no need to add a lifetime annotation.
