---
date: 2024-11-21
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create a trait Container for struct Basket and Stack


In [[2024-11-21_Generic-struct|struct Basket]] and [[2024-11-21_Make-struct-Stack-generic|struct Stack]], they have some methods with the same signatures with different implementations, so we can define a new trait to integrate these methods.

This way, we can use this trait to implement these methods for Basket and Stack instead of implementing them separately.

Define a new trait Container:

```rust
// src/container.rs
pub trait Container<T> {
//                 ^^^ list of generic types, very similar in generic struct,
//                     in fact, sometimes we treat traits as types

    fn get(&mut self) -> Option<T>; // only signature, no implementation,
                                    // because implementation is different in Basket and Stack

    fn put(&mut self, item: T);

    fn is_empty(&self) -> bool;
}

```

Then we can use this trait to implement the methods of Basket and Stack, replacing the originally implemented parts separately.

Replace the original implementation of Basket:

```rust
// src/main.rs
mod basket;
mod container;  // remember import the Container trait in main.rs
mod stack;

fn main() { }


// src/basket.rs
use super::container::Container; // include the Container trait to the scope

pub struct Basket<T> {
    item: Option<T>,
}

impl<T> Basket<T> {
    pub fn new(item: T) -> Self {  // we cannot add extra methods in the trait impl, so we need to keep this method here to impl standalone
        Basket { item: Some(item) }
    }
}

impl<T> Container<T> for Basket<T> {
//  ^^^           v             v
//   ^         ref to        ref to
//   ^            v             v
//   ^ list of generic types, specified when instantiating

    fn get(&mut self) -> Option<T> {
        self.item.take()  // write the implementation for Basket
    }
    fn put(&mut self, item: T) {
        self.item = Some(item);  // write the implementation for Basket

    }
    fn is_empty(&self) -> bool {
        self.item.is_none()  // write the implementation for Basket

    }
}

// src/stack.rs
use super::container::Container;

pub struct Stack<T> {
    items: Vec<T>,
}

impl<T> Stack<T> {
    pub fn new(items: Vec<T>) -> Self {
        Stack { items }
    }
}

impl<T> Container<T> for Stack<T> {
//                   ^^^^^^^^^^^^ impl trait for different struct, we will write different implementation for Basket and Stack
    
    fn get(&mut self) -> Option<T> {
        self.items.pop()  // write the implementation for Stack (different from Basket)
    }
    fn put(&mut self, item: T) {
        self.items.push(item);  // write the implementation for Stack (different from Basket)
    }
    fn is_empty(&self) -> bool {
        self.items.is_empty()  // write the implementation for Stack (different from Basket)
    }
}

