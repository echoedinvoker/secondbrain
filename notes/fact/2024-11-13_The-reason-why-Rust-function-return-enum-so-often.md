---
date: 2024-11-13
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# The reason why Rust function return enum so often

Enum is a bit like combining multiple structs into one type, in other languages, functions may return values of different types as follows:

```ts
function divide(a: number, b: number): number | string {
//                                            ^ not allow in RUST
  if (b === 0) {
    return "Cannot divide by zero";
  }
  return a / b;
}
```

```ts
function divide(a: number, b: number): number {
  if (b === 0) {
    throw new Error("Cannot divide by zero");
//  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ there is now error to raise in RUST
//                                            just panic, but it is only for unexpected error
  }
  return a / b;
}

```

Because of the reasons mentioned above, if you want to return different types in Rust, you can only wrap them in an enum.

```rust
use std::io::Error;

fn divide(a: i32, b: i32) -> Result<i32, Error> {
//                           ^^^^^^^^^^^^^^^^^^ instead of return different types value
//                                              return single enum with different variants
  if b == 0 {
    Err(Error::other("Cannot divide by zero"));
//  ^^^  instead of throw error, return a variant of enum Result to indicate error happened
//       `Err` just a variant label of enum Result, not a special keyword
  } else {
    Ok(a / b)
  }
}
```

