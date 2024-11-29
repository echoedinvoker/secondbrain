---
date: 2024-11-21
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Design a struct to store a String with methods get put is_empty

This struct is simply used to store a String and allows for the possibility of it being empty, so it must be wrapped in an Option.

```rust
// src/basket.rs
pub struct Basket {
    item: Option<String>,
    //    ^^^^^^^^^^^^^^ here we use Option to wrap the String because it can be empty
}

impl Basket {

    // create a fn to create a new Basket easily
    pub fn new(item: String) -> Self {
//  ^^^ we need to use these fn outside of the module, so we need to make it public
        
        Basket { item: Some(item) }
    }

    pub fn get(&mut self) -> Option<String> {
    //         ^^^^ when we get the item we want to remove it from the basket,
    //              it's better to mutate `item` instead of moving it
        self.item.take()
        //       ^^^^^^^ `.take()` is a method of Option that returns the value and replaces it with None in place
        //                                                    ^^^^^^^^^^^^^^^^^ not moving it, so no ownership is transferred
    }
    pub fn put(&mut self, item: String) {
        self.item = Some(item);
        //          ^^^^^ remember to wrap the String with Some because `item` is an Option type
    }
    pub fn is_empty(&self) -> bool {
        self.item.is_none()
        //       ^^^^^^^^^^ `.is_none()` is a method of Option that returns true if the value is None
    }
}

// src/main.rs
mod basket;
use basket::Basket;

fn main() {
    let mut b1 = Basket::new(String::from("apple"));
    //  ^^^ because following codes we use `.get()` and `.put()` which mutate the `item` field of Basket,

    println!("{}", b1.get().unwrap());  // apple
    println!("{}", b1.is_empty());  // true

    b1.put(String::from("banana"));

    println!("{}", b1.is_empty());  // false
}
```

