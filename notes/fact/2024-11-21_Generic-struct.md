---
date: 2024-11-21
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Generic struct

If we want to make [[2024-11-20_Using-generic-to-make-fn-more-flexible|this example struct]] enable to store any type of data, how to do it?

```rust
// src/basket.rs
pub struct Basket<T> {
//               ^^^ generic type list, used to specify the type of generic when creating an instance
    item: Option<T>,
    //           ^ ref to the generic type
}

impl<T> Basket<T> {
//  ^^^        ^ ref to the generic type
//   ^
//   ^ generic type list, used to specify the type of generic when implementing the instance

    pub fn new(item: T) -> Self {
    //               ^ ref to the generic type
        Basket { item: Some(item) }
    }
    pub fn get(&mut self) -> Option<T> {
    //                              ^ ref to generic type
        self.item.take()
    }
    pub fn put(&mut self, item: T) {
    //                          ^ ref to generic type
        self.item = Some(item);
    }
    pub fn is_empty(&self) -> bool {
        self.item.is_none()
    }
}

// src/main.rs
mod basket;
use basket::Basket;

fn main() {
    let b1 = Basket::new(String::from("apple"));
    //                   ^^^^^^^^^^^^^^^^^^^^^ when instantiating, specify the type of generic T to String
    let b2 = Basket::new(10);
    //                   ^^ when instantiating, specify the type of generic T to i32
    let mut b3 = Basket::new(true);
    //                       ^^^^ when instantiating, specify the type of generic T to bool

    b3.put(23); // Error: generic type T is fixed to bool when instantiating, it cannot be changed after that
}
```
