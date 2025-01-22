---
date: 2024-11-12
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Rust built in Option enum, taking care of may or may not getting thing issue

[[2024-11-11_Enum-variant-dont-need-to-tie-key-value-pairs|Enum variant don't need to tie key value pairs]]

```rust
enum Option<Value> {
    Some(Value),
    None,
}

```

In Rust, there is no `null`, `nil`, `undefined` or `None` value. Instead, Rust has the built-in `Option` enum to take care of the may or may not getting a thing issue.

Methods which may or may not return a value, they will always return an `Option` enum automatically. This way, the programmer is forced to handle the `Option` enum by `match` or `let if` before using the value.

```rust
fn main() {
    let values = vec![1, 2, 3, 4, 5];
    println!("{:?}", values.get(0)); // Some(1)
    println!("{:?}", values.get(10)); // None
    //                      ^^^ this is built-in method of Vec,
    //                          it will use Option enum to return value automatically
    //                          we need to unwrap the value from Option enum,
    //                          or we cannot really use the value
}

```

So in order to remove the hassle of Option enum wrapping, it forces us to handle the variants of Option as follows:

```rust
fn main() {
    let values = vec![1, 2, 3, 4, 5];

    match values.get(0) {
//  ^^^^^^^^^^^^^^^^^^^
        Some(value) => println!("{}", value),
//      ^^^^^^^^^^^    ^^^^^^^^^^^^^^^^^^^^
//      we need to confirm which variant of Option enum we are getting
//      and then unwrap the value from it, now we can really use the value
    
        None => println!("No value found"),
//      ^^^^    ^^^^^^^^^^^^^^^^^^^^^^^^^^
//      because impl enum needs to handle all the variants,
//      so we need to handle none value case as well, it forces us to handle the case
    }
}
```


After reading this topic, although it introduces the enum `Option`, if you still doesn't make much sense, you can refer to [[2024-11-12_Create-a-method-which-may-or-may-not-return-a-value-manually|this topic]] again. It introduces enum `Option` in different perspective.

Above, we only use the enum `Option` to handle the situation where the return value may be None. However, the `Option`` enum is not only used for return values, it can also be used for [[2024-11-12_Use-enum-Option-to-indicate-the-presence-or-absence-of-struct-field|indicate the presence or absence of struct field]]
```rust
