---
date: 2024-11-15
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Try operation

```rust
    let text = fs::read_to_string("logs.txt")?;
    //         ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^ 
    //                    ^                  `?` is try operation, it can be added to the end of a statement that returns `Result`
    //                    ^
    //         this method returns `Result` value

```

**when result is `Ok`**


```rust
    let text = String;
    //         ^^^^^^ `?` will unwrap the `Ok` automatically

```

**when result is `Err`**
```rust
    return Error;
//  ^^^^^^^^^^^^^ `?` will generate an Error type value and return it early
    
```

Usually, the try operation is used in conjunction with [[2024-11-15_Function-main-can-return-Result|the Result return type of the main function]] to handle errors generated in the main function.

[[2024-11-15_Resolved-nested-match-by-try-operation|here]] is an real world example to resolve nested match by try operation.
