---
date: 2024-11-17
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Why warning when set arg of fn as vector ref?

When we define an argument of a function as a reference of a vector, some IDEs may display the following warning.

```rust
fn print_elements(elements: &Vec<String>) {
//                          ^^^^ 1. writing `&Vec` instead of `&[_]` involves a new object where a slice will do
//                               for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#ptr_arg
//                               `#[warn(clippy::ptr_arg)]` on by default [ptr_arg]

    elements
        .iter()
        .map(|el| format!("{} {}", el, el))
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

The warning above simply suggests that we change the argument type from a reference of vector to a [[2024-11-17_Vector-slice|vector slice]], as this allows the function to accept both a reference of vector and a vector slice as parameters, making it more flexible.


```rust
fn print_elements(elements: &[String]) {
//                          ^^^^^^^^^ change type to `vector slice`

    elements
        .iter()
        .map(|el| format!("{} {}", el, el))
        .for_each(|el| println!("{}", el));
}

fn main() {
    let colors = vec![
        String::from("Red"),

        // assume we only want to pass `Green` and `Blue` to the function and no ownership transfer
        String::from("Green"),
        String::from("Blue"),
    ];

    print_elements(&colors[1..]);
    //             ^^^^^^^^^^^^ add [1..] to pass a vector slice, it is portion of the vector and it's also reference

    print_elements(&colors);
    //             ^^^^^^^ pass a reference of the vector is still valid
}

```
