---
date: 2024-11-08
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Given the ownership system, how do we write useful code?

Bellow is one of [[2024-11-08_Visualize-Complex-Example-of-rule-1-and-2-of-twelve-rules|Visualize Complex Example of rule 1 and 2 of twelve rules]]:

```rust
fn print_account(account: Account) {
  println!("Account ID: {:#?}", account);
}

fn main() {
  let account = Account::new(1, String::from("Alice"));

  print_account(account);
  print_account(account); // Error: borrow of moved value: `account`
}
```

There are two options to solve this problem:


1. Manually move values back and forth between different owners.

Very tedious and error-prone, we won't do it in real-world projects. But [[2024-11-08_Move-values-back-and-forth-between-different-owners|here]] is an example of how to do it, which can help us understand the concept better.


2. Use the borrowing system.
