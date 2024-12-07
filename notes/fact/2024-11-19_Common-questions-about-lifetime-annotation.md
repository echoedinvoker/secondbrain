---
date: 2024-11-19
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Common questions about lifetime annotation

In [[2024-11-19_Big-assumption-when-fn-args-and-return-type-are-all-reference|this topic]], we introduced the lifetime annotation in Rust. In this topic, we will discuss some common questions about lifetime annotations.


## Question: Why do Rust compilers make a BIG assumption?

This ensures that the function does not generate any new data when both the arguments and return are references.


## Question: Why do we need to set the return Ref and a certain argument's Ref to the same lifetime annotation?

```rust
fn split<'a>(s: &'a str, pattern: &str) -> &'a str { ... }
//      ^^^^     ^^                         ^^

fn main {
    let sentence = "hello world";
    let result;

    {
      let pattern = " ";
      result = split(sentence, pattern);

      // sentence still alive because it is declared outside the block
      // result is tied to sentence's lifetime by the lifetime annotation
    }

    println!("{}", result);
    //             ^^^^^^ works!
}
```

```rust
fn split<'a>(s: &str, pattern: &'a str) -> &'a str { ... }
//      ^^^^                    ^^          ^^

fn main {
    let sentence = "hello world";
    let result;

    {
      let pattern = " ";
      result = split(sentence, pattern);

      // pattern out of scope, and the value pointed by pattern is dropped
      // result is tied to pattern's lifetime by the lifetime annotation, so it is invalid here
    }

    println!("{}", result);
    //             ^^^^^^ error: use of possibly invalid value
}

```
Above, we must use `lifetime annotation` to tell the compiler which argument `result` is related to.


## Question: Why doesn't the Rust compiler automatically infer the lifetime of a returned Ref?

When we use a library, the documentation may only have the signature of the function without any content as follows:

```rust
fn split<'a>(s: &'a str, pattern: &str) -> &'a str { ... }
//                                                   ^^^ fn body is not provided in the documentation
```
In this situation, `lifetime annotation` is necessary because without it, we would have no way of knowing how long the lifetime of the return Ref is.
