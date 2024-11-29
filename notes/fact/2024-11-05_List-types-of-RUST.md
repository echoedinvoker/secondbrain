---
date: 2024-11-05
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# List types of RUST

There are two types to represent a list in Rust:

1. Array

It is fixed in size and elements are of the same type.

We can define an array as:

```rust
  let arr: [i32; 5] = [1, 2, 3, 4, 5];
  ```
5 is the size of the array. It is a fixed size.


2. Vector

It is dynamic in size and elements are of the same type. So it can grow or shrink.

We can define a vector as:

```rust
  let vec: Vec<i32> = vec![1, 2, 3, 4, 5];

```
There is no size parameter in the vector. It is dynamic in size.


**Why RUST has two types of lists?**

Array is slightly faster than a vector because it is fixed in size and stored in the stack. Vector is stored in the heap and is slower than an array.

But performance is not the main reason for having two types of lists in Rust. The main reason is to give more message to other developers about the intention of the code. If you use an array, you are telling other developers that the size of the list is fixed. If you use a vector, you are telling other developers that the size of the list can grow or shrink.
