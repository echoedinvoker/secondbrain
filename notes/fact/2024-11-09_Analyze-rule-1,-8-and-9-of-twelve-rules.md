---
date: 2024-11-09
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Analyze rule 1, 8 and 9 of twelve rules

These rules are about [[2024-11-09_Lifetimes-and-Generic-Lifetimes|lifetimes]] of owner and its value in Rust. 

From [[2024-11-07_Twelve-rules-of-writing-Rust-script|twelve rules of writing Rust script]], rule 1, 8 and 9 are:


1. Every value is 'owned' by a single variable, struct, vector, etc at a time

8. When a variable goes out of scope, the value owned by it is dropped (cleaned up in memory)

9. Values can't be dropped if there are still active references to it


## Example to rule 1 and 8

```rust
fn create_and_print_account() {
    let account = Account::new("John Doe", 1000);
    println!("Account: {:?}", account);


    // Here
    // binding `account` goes out of scope, which is the owner of the Account value
    // because of rule 8, the Account value is dropped from memory when
    // its owner goes out of scope
}

fn main() {
    create_and_print_account();
}

```


## Example to rule 9

```rust
fn create_and_print_account() -> &Account {
//                            ^^^^^^^^^^^ we try to return reference of Account value
//                                        to wish to keep it alive after the owner goes
    let account = Account::new("John Doe", 1000);
    println!("Account: {:?}", account);

    &account
//  ^^^^^^^^ create a reference to the Account value and return it
//           Error: because the owner binding `account` of Account value goes out of scope
//                  the Account value is dropped from memory, so returning a reference to it
//                  is not allowed, because it refer to NOTHING
//                  (We conclude it to rule 9)
}

fn main() {
    let account = create_and_print_account();
//  ^^^^^^^^^^^^^^ not work

    println!("Account: {:?}", account);
//  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ not work
}

```
