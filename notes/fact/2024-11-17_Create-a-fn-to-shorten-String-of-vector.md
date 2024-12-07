---
date: 2024-11-17
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create a fn to shorten String of vector

To change the elements in the vector, according to this [[2024-11-17_Three-Iterator-pointer-type|topic]], our iterator must use `iter_mut()` to obtain a mutable reference.

In closure, we use [[2024-11-17_String-truncate|String truncate]] to change the `String` value itself instead of creating a new vector struct or String.

```rust
fn shorten_strings(elements: &mut [String]) {
//                           ^^^^^^^^^^^^^ &mut Vec<String> can also be replaced
//                                         with &mut [String] (vector slice)
//                                         which is more flexible    
    
    elements.iter_mut().for_each(|el| el.truncate(1));
    //      ^^^^^^^^^^^           ^^    ^^^^^^^^^^^^ use truncate to shorten the String value itself  
    //      instead of .iter(), .iter_mut()
    //      is used to obtain a mutable reference
}

fn main() {
    let mut colors = vec![
        String::from("Red"),
        String::from("Green"),
        String::from("Blue"),
    ];

    shorten_strings(&mut colors);
    //              ^^^^^^^^^^^ create a mutable reference to the vector and pass it to the function
}

```
