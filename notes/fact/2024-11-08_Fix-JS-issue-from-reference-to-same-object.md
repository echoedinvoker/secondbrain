---
date: 2024-11-08
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Fix JS issue from reference to same object

Here we try to fix [[2024-11-08_A-JS-demo-of-a-nasty-bug-that-Rust-ownership-want-to-avoid|a nasty JS bug from reference to the same object]].

In other words, this bug occurs when a reference pointing to the same object is modified, causing other references pointing to the same object to be affected. For the codes which use other references to the same object, that modification is unexpected and causes bugs.

Our purpose is to remove any unexpected modification.

There are two ways to prevent bugs happening from reference to the same object:


## option 1: make referred object immutable (read-only)

![refered-obj-immutable.png](../assets/imgs/refered-obj-immutable.png)

Although this option cannot modify the referred object anymore, it can prevent bugs from occurring and still allow multiple references to point to the same object.

We can turn it to a formula:

>Multiple things can refer to a value at the same time, but the reference ensures the value is read-only.

This fix resulted in 3, 5, and 6 in the [[2024-11-07_Twelve-rules-of-writing-Rust-script|12 rules]].


## option 2: clone the referred object, let each reference point to a different object

![clone-referred-obj.png](../assets/imgs/clone-referred-obj.png)

We must clone a new object for each reference in this method to avoid mutual influence between references. Although this will increase memory usage, it can prevent bugs while still allowing the ability to modify the object.

We can turn it to a formula:

>A value can **only** be updated when there are no read-only references to it.

This fix resulted in 1, 5, and 6 in the [[2024-11-07_Twelve-rules-of-writing-Rust-script|12 rules]].
