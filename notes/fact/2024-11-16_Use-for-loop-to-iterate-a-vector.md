---
date: 2024-11-16
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Use for loop to iterate a vector

```rust
fn print_elements(elements: &Vec<String>) {
//                          ^^^^^^^^^^^^ this fn don't store the vector, so we use a reference
    
    for element in elements {
    //  ^^^^^^^ `for in` generate an Iter value and execute the `.next()` automatically
    //          if `.next()` return Some, it will unwrap it and assign it to the variable `element`
    //          if `.next()` return None, it breaks the loop
      
        println!("{}", element);
        //             ^^^^^^^ so we can use it here
    }
}

fn main() {
    let colors = vec![
        String::from("Red"),
        String::from("Green"),
        String::from("Blue"),
    ];

    print_elements(&colors);
//  ^^^^^^^^^^^^^^^^^^^^^^^ pass the reference of the vector into the function we created above
}

```

```bash
~/D/g/s/r/iter > cargo run -q
Red
Green
Blue

```

Therefore, `for in` actually uses the `Iter` struct to iterate over the elements of a vector in a way commonly seen in other languages.
