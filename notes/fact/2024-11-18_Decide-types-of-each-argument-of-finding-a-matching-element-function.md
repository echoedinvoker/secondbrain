---
date: 2024-11-18
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Decide types of each argument of finding a matching element function

For [[2024-11-18_Create-a-fn-to-find-a-matching-element-or-return-a-fallback|creating a fn to find a matching element or return a fallback]], our first step is to decide the types of each argument of the function.


```rust
fn find_color_or(colors: __, search: __, fallback: __) -> __ {
//                       ^^          ^^            ^^     ^^   consider types

    // ...   
}

```

We use the method of elimination to find the types of arguments:

1. store value? No --> exclude value type
2. mutate value? No --> exclude mutable reference type
3. read-only reference

Then we got types as follows:

```rust
fn find_color_or(colors: &[String], search: &str, fallback: &str) -> ___ {
//                       ^^^^^^^^^          ^^^^            ^^^^     ^^^ still need to decide type of return value

    // ...   
}

```
Regarding the return type, we must consider how the user will use this function.

If we use `&str` as the return type:

```rust
fn find_color_or(colors: &[String], search: &str, fallback: &str) -> &str { ... }
//                                                                   ^^^^

fn get_color() -> &str {
    let colors = vec!["red".to_string(), "green".to_string()];  // if user defines colors inside this fn
    let search = "blue";
    let fallback = "black";
    find_color_or(&colors, &search, &fallback)  // and also use fn `find_color_or` inside this fn

    // `colors` is out of scope here, so its value will be dropped from memory
    // but `find_color_or` returns a reference to the value inside `colors`, which is now dropped
    // then we will get a dangling reference error
}

fn main() {
    let color = get_color();
    println!("Color: {}", color);
}

```

So we better use `String` as the return type in this case:

```rust
fn find_color_or(colors: &[String], search: &str, fallback: &str) -> String { ... }
//                                                                   ^^^^^^ which is more safe

```

After types are done, we can now start to implement the function [[2024-11-18_Iterate-to-find-match-element-or-return-fallback|content]].
