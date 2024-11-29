---
date: 2024-11-21
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Design a struct to store a vector of String with methods get, put and is_empty

It should be very similar to [[2024-11-21_Design-a-struct-to-store-a-String-with-methods-get-put-is_empty|this example]], but with a VECTOR of strings.

Because we are using a vector, we don't need to use `Option` a lot to represent the empty state. The vector itself can be empty to represent that state.

```rust
// src/stack.rs
pub struct Stack {
    items: Vec<String>,
    //     ^^^^^^^^^^^ we don't need Option here because when there is empty, it should be a empty vector 
    //                 so vector type is enough to represent empty state
}

impl Stack {
    pub fn new(items: Vec<String>) -> Self {
        Stack { items }
    }
    pub fn get(&mut self) -> Option<String> {
    //                       ^^^^^^ we only need to use Option here because it has change that `.pop()` can return None when the vector is empty
        self.items.pop()
        //        ^^^^^^ `.pop()` is a common vector method to remove and return the last element from the vector
        //               and it uses Option to wrap the return value because there is a chance that the vector is empty
    }

    pub fn put(&mut self, item: String) {
        self.items.push(item);
        //        ^^^^^^^^^^^^ `.push()` is a common vector method to add an element to the end of the vector
    }

    pub fn is_empty(&self) -> bool {
        self.items.is_empty()
        //        ^^^^^^^^^^^ instead of `.is_none()` in previous example for Option::None,
        //                    we use `.is_empty()` to check if the vector is empty here
    }
}

// src/main.rs
mod stack;
use stack::Stack;

fn main() {
    let mut s = Stack::new(vec![String::from("apple"), String::from("banana")]);
    println!("Is the stack empty? {}", s.is_empty());  // false

    let item1 = s.get();
    println!("Popped item: {:?}", item1); // Popped item: Some("banana")

    let item2 = s.get();
    println!("Popped item: {:?}", item2); // Popped item: Some("apple")

    let item3 = s.get();
    println!("Popped item: {:?}", item3); // Popped item: None

    println!("Is the stack empty? {}", s.is_empty()); // true

    s.put(String::from("cherry"));

    println!("Is the stack empty? {}", s.is_empty()); // false
}
