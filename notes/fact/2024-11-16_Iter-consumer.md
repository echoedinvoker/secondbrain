---
date: 2024-11-16
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Iter consumer

Iter consumer refers to some methods of Iter that will automatically call `.next()`, such as `for_each`, `map`, `filter`, and so on.

For example, we can use `for_each` to make [[2024-11-16_Use-for-loop-to-iterate-a-vector|this example]] more concise:

```rust
fn print_elements(elements: &Vec<String>) {

    elements.iter() // Create an Iter value
        .for_each(  // Use its consumer method `for_each`,
                    // it will simply call .next() automatically until this Iter is exhausted

            |el| println!("{}", el)  // this consumer method accepts a closure as argument
                                    // It will be called for each element in the Iter
        );
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

We use [[2024-11-16_Closure-in-Rust|closure]] technique to pass a function to `for_each` method. This function will be called for each element in the Iter.

```bash
~/D/g/s/r/iter > cargo run -q
Red
Green
Blue

```

