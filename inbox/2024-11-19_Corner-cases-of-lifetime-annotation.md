---
date: 2024-11-19
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Corner cases of lifetime annotation


## There are two situations where we can omit the lifetime annotation

**1. fn takes ONE ref + any number of values + returns a ref**

Because it's obvious that the returned reference is tied to the only input reference.

```rust
fn last_language(languages: &[String]) -> &str { ... }  // return Ref tied to `languages` automatically
```
```rust
fn generate(set: &[i32], range: i32) -> &str { ... }  // return Ref tied to `set` automatically
```
```rust
fn leave(message: &Message, text: String) -> &str { ... }  // return Ref tied to `message` automatically
```


**2. method returns a ref**

No matter how many references the method takes, the return reference will automatically be bound to the first reference `&self`.

```rust
struct Bank {
    name: String,
}

impl Bank {
    fn get_name(&self, default_name: &str) -> &str { ... }  // return Ref tied to `self` automatically
}
```

If we need to bind the return reference with an argument ref other than &self, we must manually add a lifetime annotation.

```rust
struct Bank {
    name: String,
}

impl Bank {
    fn get_name<'a>(&'a self, default_name: &'a str) -> &'a str { ... }  // return Ref tied to `default_name` by lifetime annotation
}

```


## In Rust documentation, it prefer to use `elided`, `elision` instead of `omitted` or `removal`

So above we mentioned that we can `omit` lifetime annotation, but in reality in Rust documentation we will only see that we can `elide` lifetime annotation.

`Elide` and `elision` are very rarely used words that we almost never use in everyday conversation. Some members of the Rust documentation team decided to use this word, which the lecturer feels is a very bad decision.
