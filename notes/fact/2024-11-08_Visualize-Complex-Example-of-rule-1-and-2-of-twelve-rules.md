---
date: 2024-11-08
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Visualize Complex Example of rule 1 and 2 of twelve rules

[[2024-11-08_Example-of-rule-1-and-2-of-twelve-rules|Example of rule 1 and 2 of twelve rules]] is most simple example of rule 1 and 2 of twelve rules because its owners are always bindings.

In fact, owners can be any thing that can contain values such as data structure, struct or even a function. And these owners make the ownership and movement mechanism of Rust more complex a lot!

Below we list many examples and visualize them to help you better understand Rust's ownership and movement mechanisms.

## Example 1

```rust
fn print_account(account: Account) {
  println!("Account ID: {:#?}", account);
}

fn main() {
  let account = Account::new(1, String::from("Alice"));
}

```
![example-1.png](../assets/imgs/example-1.png)
```rust
fn print_account(account: Account) {
  println!("Account ID: {:#?}", account);
}

fn main() {
  let account = Account::new(1, String::from("Alice"));

  print_account(account);
  print_account(account);
}
```
![example-2.png](../assets/imgs/example-2.png)

So the second function `print_account` point to the binding `account` which has no value anymore. This will cause a compile error.


## Example 2

```rust
fn print_account(account: Account) {
  println!("Account ID: {:#?}", account);
}

fn main() {
  let account = Account::new(1, String::from("Alice"));
}
```

![example-3.png](../assets/imgs/example-3.png)

```rust
fn print_account(account: Account) {
  println!("Account ID: {:#?}", account);
}

fn main() {
  let account = Account::new(1, String::from("Alice"));

  let list_of_accounts = vec![account];

  println!("{:#?}", account); // account value already moved, compile error
}
```
![example-4.png](../assets/imgs/example-4.png)


## Example 3

```rust
fn main() {
  let bank = Bank::new();
}
```
![example-3-1.png](../assets/imgs/example-3-1.png)

```rust
fn main() {
  let bank = Bank::new();

  let accounts = bank.accounts;

  println!("{:#?}", bank.accounts); // bank.accounts value already moved, compile error
}
```
![example-3-2.png](../assets/imgs/example-3-2.png)


## Example 4

```rust
fn print_account(account: Account) {
  println!("Account ID: {:#?}", account);
}

fn main() {
  let account = Account::new(1, String::from("Alice"));
}
```
![example-4-1.png](../assets/imgs/example-4-1.png)

```rust
fn print_account(account: Account) {
  println!("Account ID: {:#?}", account);
}

fn main() {
  let account = Account::new(1, String::from("Alice"));

  print_account(account);

  println!("{:#?}", account.holder); // account value already moved, compile error
}
```
![example-4-2.png](../assets/imgs/example-4-2.png)


In the examples above, the reference points to either `NO VALUE!` or to a place with a value. But if the reference points to a data structure with missing values, will an error occur?

Check [[2024-11-08_Rust-NOT-allow-using-partial-moved-value|here]] for the detailed explanation.
```
