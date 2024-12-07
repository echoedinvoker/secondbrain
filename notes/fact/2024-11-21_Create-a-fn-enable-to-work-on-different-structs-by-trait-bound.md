---
date: 2024-11-21
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create a fn enable to work on different structs by trait bound

From [[2024-11-21_Create-a-trait-Container-for-struct-Basket-and-Stack|this topic]], we created a trait `Container` and implemented it for `Basket` and `Stack` structs. Then, we can create functions that work on both types by using the trait bound as below:

```rust
mod basket;
mod container;
mod stack;
use basket::Basket;
use container::Container;
use stack::Stack;

fn add_string<T: Container<String>>(container: &mut T, item: String) {
//             ^^^^^^^^^^^^^^^^^^^ trait bound, it makes sure this fn only accepts `Basket` and `Stack` types input to argument `container`
//                                 and because `Container` is a generic trait, when it is used in trait bound, we need to specify the type
    
    container.put(item);
    //       ^^^^^^^^^^ because generic type T is restricted by the trait bound, it makes sure that `container` has the `put` method
}

fn main() {
    let mut b1 = Basket::new(String::from("banana"));
    let b2 = Basket::new(10);
    let b3 = Basket::new(false);

    let mut s1 = Stack::new(vec![
        String::from("a"),
        String::from("b"),
        String::from("c"),
    ]);
    let s2 = Stack::new(vec![1, 2, 3]);
    let s3 = Stack::new(vec![true, false]);

    // fn add_string can work on both Basket and Stack as below:
    add_string(&mut b1, String::from("apple"));
    add_string(&mut s1, String::from("d"));
    //                  ^^^^^^^^^^^^^^^^^ because trait bound is `Container<String>`, from script `impl<T> Container<T> for Basket<T>`
    //                                    if Container<String> --reverse--> impl<String> <--ref to-- Basket<String>
    //                                    so arg `container` only accepts `Basket<String>` and `Stack<String>`
}

```

Therefore, the conclusion is that after implementing a trait in multiple structs, we can use trait bounds to create functions that can be applied to multiple structs.
