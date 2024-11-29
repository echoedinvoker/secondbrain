---
date: 2024-11-13
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Tuple in Rust


## Demo of tuple

If the data structure of the struct is relatively simple, we can use a tuple to replace the struct, which will make the code more concise.

```rust
#[derive(Debug)]
struct Rgb {
    red: u8,
    blue: u8,
    green: u8,
}

fn make_rbg(red: u8, blue: u8, green: u8) -> Rgb {
    Rgb { red, blue, green }
}

fn main() {
    let orange = make_rbg(255, 165, 0);
    let purple = make_rbg(128, 0, 128);

    println!("{:?}", orange); // Rgb { red: 255, blue: 0, green: 165 }
    println!("{:?}", purple); // Rgb { red: 128, blue: 0, green: 128 }
}

```

We can replace struct with tuple to make our codes more concise:

```rust
type Rgb = (u8, u8, u8); // replace struct with tuple, which is more concise
                        // but need to remember the meaning of each element by ourselves

fn make_rbg(red: u8, blue: u8, green: u8) -> Rgb {
//                                           ^^^ this is tuple value instead of struct value right now
    (red, blue, green)
//  ^^^^^^^^^^^^^^^^^^ tuple value is wrapped by parentheses, and don't need to specify field names or type name
}

fn main() {
    let orange = make_rbg(255, 165, 0);
    let purple = make_rbg(128, 0, 128);

    println!("{:?}", orange);  // (255, 165, 0)  so tuple value's type is only `tuple`, 
    println!("{:?}", purple);  // (128, 0, 128)  don't need to specify which name it is like struct
}                              //                it makes code more concise and flexible
```

## Summary of the differences between tuple and struct

- Tuple has no field name, only field value, so it is more concise than struct
- Tuple has no type name, only tuple, so it is more flexible than struct
- Tuple is similar to array, both of them are fixed length, but tuple can store different types of data, while array can't, that's also the reason why we use tuple to replace struct in some cases but not array.

