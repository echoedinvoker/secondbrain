---
date: 2024-11-08
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Rust NOT allow using partial moved value

This is a more specific example from [[2024-11-08_Visualize-Complex-Example-of-rule-1-and-2-of-twelve-rules|Visualize Complex Example of rule 1 and 2 of twelve rules]]

```rust
fn print_account(account: Account) {
  println!("Account ID: {:#?}", account);
}

fn print_holder(holder: String) {
  println!("{}", holder);
}

fn main() {
  let account = Account::new(1, String::from("Alice"));
}
```
![example-5-1.png](../assets/imgs/example-5-1.png)

```rust
fn print_account(account: Account) {
  println!("Account ID: {:#?}", account);
}

fn print_holder(holder: String) {
  println!("{}", holder);
}

fn main() {
  let account = Account::new(1, String::from("Alice"));

  print_holder(account.holder);

  print_account(account); // account.holder value already moved
                          // Rust not allow partial moved value, so still compile error
}
```

![example-5-2.png](../assets/imgs/example-5-2.png)



Therefore, if the reference points to an incomplete data structure, Rust will also generate a compile error.
