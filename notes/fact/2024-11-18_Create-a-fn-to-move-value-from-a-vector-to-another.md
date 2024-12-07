---
date: 2024-11-18
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create a fn to move value from a vector to another

Based on [[2024-11-18_corner-cases-of-into_iter-method|this topic]], we can use `into_iter()` to move the elements from one vector to another, but it must be called on a value, not a reference.

```rust
fn move_elements(vec_a: Vec<String>, vec_b: &mut Vec<String>) {
//                      ^^^^^^^^^^^ we take ownership of this vector
//                                  so it must be a value, not a reference
    
    vec_a.into_iter().for_each(|el| vec_b.push(el));
    //   ^^^^^^^^^^^^           ^^ .into_iter() is called on a value, so that 
    //                             the `el` take ownership of the element
}

fn main() {
    let colors = vec![
        String::from("Red"),
        String::from("Green"),
        String::from("Blue"),
    ];

    let mut destination = vec![];

    move_elements(colors, &mut destination);
    //            ^^^^^^ move ownership of `colors` to `move_elements` for `into_iter()`
    //                   so we can't use `colors` in main scope anymore

    println!("Destination: {:?}", destination);
}

```

