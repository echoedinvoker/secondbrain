---
date: 2024-11-07
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Twelve rules of writing Rust script

From [[2024-11-07_Three-core-systems-of-Rust|three core systems of Rust]], we can derive 12 rules of writing Rust script:

## 12 rules of Rust
**Ownership rules**

1. Every value is 'owned' by a single variable, struct, vector, etc at a time

2. Reassigning the value to another variable, passing it to a function, putting it into a vector, etc, moves the value. The old variable can't be used anymore!

**Borrowing rules**

3. You can create many read-only references to a value that exist at the same time

4. You can't move a value while a ref to the value exists

5. You can make a writable (mutable) reference to a value only if there are no read-only references currently in use. One mutable ref to a value can exist at a time

6. You can't mutate a value through the owner when any ref (mutable or immutable) to the value exists

7. Some types of values are copied instead of moved (numbers, bools, chars, arrays/tuples with copyable elements)

**Lifetime rules**

8. When a variable goes out of scope, the value owned by it is dropped (cleaned up in memory)

9. Values can't be dropped if there are still active references to it

10. References to a value can't outlive the value they refer to

**Other rules**

11. These rules will dramatically change how you write code (compared to other languages)

12. When in doubt, remember that Rust wants to minimize unexpected updates to data


## Too many rules to remember...

Only need to remember the first thing:

>Above all, Rust want to avoid 'unexpected updates'

You can figure out almost everything else from there by yourself!

You can check [[2024-11-08_A-JS-demo-of-a-nasty-bug-that-Rust-ownership-want-to-avoid|this]] example to get more sense of what 'unexpected updates' mean. 
