---
date: 2024-11-17
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Iterator Adapter

From [[2024-11-16_Iter-consumer|this example]], we want to print each color twice at same line. We can just change a bit of the code to achieve this as below:

```rust
fn print_elements(elements: &Vec<String>) {
    elements.iter().for_each(|el| println!("{} {}", el, el));
    //                                        ^^^     ^^^^  simply change closure of `for_each` to print twice
}

fn main() {
    let colors = vec![
        String::from("Red"),
        String::from("Green"),
        String::from("Blue"),
    ];

    print_elements(&colors);
}

```

Or we can use Iter Adapter such as `map` to achieve the same result more elegantly:

```rust
fn print_elements(elements: &Vec<String>) {
    elements
        .iter()
        .map(|el| format!("{} {}", el, el))  // Use `map` to transform each element
        .for_each(|el| println!("{}", el));
}

fn main() {
    let colors = vec![
        String::from("Red"),
        String::from("Green"),
        String::from("Blue"),
    ];

    print_elements(&colors);
}

```

`map` is one of the Iter Adapter that will transform each element of the Iter into another value. But it will not consume the Iter, which means it won't call `.next()` automatically. We need to connect it with a Iter consumer method, such as `for_each`, to make it work.

```rust
fn print_elements(elements: &Vec<String>) {
    elements
        .iter()
        .map(|el| format!("{} {}", el, el)) // If we remove `for_each` line, iteration will not happen
                                            // and Rust compiler will issue a warning:

                                                  // 1. unused `std::iter::Map` that must be used
                                                  // iterators are lazy and do nothing unless consumed
                                                  // `#[warn(unused_must_use)]` on by default [unused_must_use]

}

fn main() {
    let colors = vec![
        String::from("Red"),
        String::from("Green"),
        String::from("Blue"),
    ];

    print_elements(&colors);
}

```

So Iter Adapter in Rust must be inserted between Iter and Iter consumer pipeline to work, which is different from other languages that have `map` method built-in to the prototype of Array or List.

