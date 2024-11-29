---
date: 2024-11-07
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create struct Account and Bank with fields definition

We want to simulate a bank system, so we need to define two structs: `Account` and `Bank`.
Creating structs and declaring fields is the first step to define the data structure of the system.

```rust
#[derive(Debug)]  // sometimes we need to print instances for debugging purposes, so we add this attribute
struct Account {
    id: u32, // unsigned integer
    balance: i32, // It's common to use integers to represent currency values, for example, 1024 is $10.24
                  // Not recommended to use floating-point numbers because of precision issues
    holder: String,
}

#[derive(Debug)]  // sometimes we need to print instances for debugging purposes, so we add this attribute
struct Bank {
    accounts: Vec<Account>,  // declare a field `accounts`, which value is a vector of `Account` instances
    //            ^^^^^^^  here we use struct Account as a type for field definition
}

fn main() {}

```

