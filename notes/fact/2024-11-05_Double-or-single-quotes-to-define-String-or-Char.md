---
date: 2024-11-05
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Double or single quotes to define String or Char

In Rust, we must use double quotes to define a String and single quotes to define a Char.


**String**

```rust
let s = "Hello, World!";
```


**Char**

```rust
let c = 'c';
//       ^  inside single quotes, there is only ONE character.
```

If we try to put more than one character inside single quotes, we will get a compilation error.
