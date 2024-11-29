---
date: 2024-11-17
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Vector slice

`Vector slice` is very similar to `string slice`, but it is the `vector` version. [[2024-11-14_Multiply-String-types-in-Rust|link of string types topic]]

It is designed to a issue from reference of vector, let's see the example:

```rust
fn print_elements(elements: &Vec<String>) { ... }
//                          ^^^^^^^^^^^^ if use reference of vector as argument of fn

fn main() {
    let colors = vec![
        String::from("Red"),
        String::from("Green"),
        String::from("Blue"),
    ];

    print_elements(&colors);
}

```

Can be visualized as the following diagram:

![ref-vector.png](../assets/imgs/ref-vector.png)


Assuming I only need to pass a portion of the elements in colors to the `print_elements` function, I must do it as shown in the following diagram:

![slice-ref-vector.png](../assets/imgs/slice-ref-vector.png)

In the above image, we can see that we copied the vector structure of colors and the data it points to. The process is very complex and inefficient. In fact, we can use `vector slice` to solve this problem as shown in the following image:

![vector-slice.png](../assets/imgs/vector-slice.png)
The concept of `Vector slice` is very similar to `string slice`, but it is the `vector` version. As shown in the above figure, it directly references the original vector items, instead of copying a new vector or data.

We can rewrite our codes using `vector slice` as follows:

```rust
fn print_elements(elements: &[String]) {
//                          ^^^^^^^^^ &[ ] means `vector slice`
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

    print_elements(&colors[1..3]);
    //             ^^^^^^^^^^^^^  &<binding name>[start..end] means to create a vector slice value
    //                            start index is included, but end index is excluded
}

```

## Design a fn with reference of vector

If we design a function and define its argument type as a reference of vector, some IDEs may suggest changing it to a vector slice, with detailed reasons explained in this [[2024-11-17_Why-warning-when-set-arg-of-fn-as-vector-ref?|topic]].
