---
date: 2024-11-21
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Make struct Stack generic

In [[2024-11-21_Design-a-struct-to-store-a-vector-of-String-with-methods-get,-put-and-is_empty|this case]], we can only store vector of String to the Stack, but we want to store a vector of any type to the Stack now. So we need to make the struct Stack generic as below:

```rust
pub struct Stack<T> {
//              ^^^
    items: Vec<T>,
    //         ^
}

impl<T> Stack<T> {
//  ^^^       ^
    pub fn new(items: Vec<T>) -> Self {
    //                    ^
        Stack { items }
    }
    pub fn get(&mut self) -> Option<T> {
    //                              ^
        self.items.pop()
    }

    pub fn put(&mut self, item: T) {
    //                          ^
        self.items.push(item);
    }

    pub fn is_empty(&self) -> bool {
        self.items.is_empty()
    }
}

```
It is very similar to [[2024-11-21_Generic-struct|this case]], so I don't explain it again. 
