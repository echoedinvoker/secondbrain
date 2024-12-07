---
date: 2024-11-16
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create a function to print each element of a given vector

It is obvious that this requires an iteration process. We can achieve this by generating an Iter type value as in [[2024-11-16_Create-Iter-from-a-vector-and-iterate-it-manually|this example]], but we usually do not use `next` to manually traverse, instead we have the following two options:


## Option 1: Use for loop

Which is the most common way to iterate over a collection in Rust.

[[2024-11-16_Use-for-loop-to-iterate-a-vector|here]] is an example and detailed explanation for this option.


## Option 2: Use iterator adapter and consumers like `for_each`, `collect`, `map`, etc.
