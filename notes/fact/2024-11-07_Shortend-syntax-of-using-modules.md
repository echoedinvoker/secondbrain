---
date: 2024-11-07
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Shortend syntax of using modules

![external-modules.png](../assets/imgs/external-modules.png)

```rust
fn main() {
  let rng = rand::thread_rng();
  let rand_number = rand::random();
  let rand_u64 = rand::rngs::OsRng.next_u64();
  //             ^^^^^^ We duplicated `rand::` 3 times
}
```
To avoid the duplication, we can use the `use` keyword to bring the module into scope.
>`use` keyword is NOT the same as `import` in Python or `require` in JavaScript. It's used to bring a module into scope, not to import a module.

```rust
use rand::thread_rng;
use rand::random;
use rand::rngs::OsRng;


fn main() {
  let rng = thread_rng();
  //        ^^^^^^^^^^^^^ We don't need to prefix `rand::` anymore
  let rand_number = random();
  let rand_u64 = OsRng.next_u64();
}

```

We can further shorten the code of `use` section as below:

```rust
use rand::{thread_rng, random, rngs::OsRng};
//        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ use curly braces to import multiple things at once

fn main() {
  let rng = thread_rng();
  let rand_number = random();
  let rand_u64 = OsRng.next_u64();
}

```
