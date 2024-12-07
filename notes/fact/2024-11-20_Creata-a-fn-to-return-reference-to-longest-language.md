---
date: 2024-11-20
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Creata a fn to return reference to longest language

```rust
fn longest_language<'a>(lang_a: &str, lang_b: &str) -> &'a str {
//                              ^^^^          ^^^^  which one should be annotated?
    
    if lang_a.len() > lang_b.len() {
        lang_a  // should add lifetime annotation to the first argument
    } else {
        lang_b  // should add lifetime annotation to the second argument
    }
}

fn main() {
    println!("Last language: {}", longest_language("Rust", "Python"));
}

```

Based on [[2024-11-19_Big-assumption-when-fn-args-and-return-type-are-all-reference|this topic]], above fn need to add lifetime annotation to tied return ref to one of the argument ref. But which one should be annotated? Both arguments have chance to be the longest language. So, how to annotate the return ref? 

```rust
fn longest_language<'a>(lang_a: &'a str, lang_b: &'a str) -> &'a str {
//                               ^^               ^^ add lifetime annotation to both arguments
//                                                   it means return ref is tied to one of them (we don't know which one, only know that it's one of them)
    
    if lang_a.len() > lang_b.len() {
        lang_a
    } else {
        lang_b
    }
}

fn main() {
    println!("Last language: {}", longest_language("Rust", "Python"));
}

```
