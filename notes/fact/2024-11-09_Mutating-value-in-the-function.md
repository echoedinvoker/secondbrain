---
date: 2024-11-09
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Mutating value in the function

There are two ways to mutate a value in a function:

## Option 1: move ownership forth and back (not recommended)

```rust
fn change_account(mut account: Account) -> Account {
//                ^^^^^^^^^^^^^^^^^^^^ take ownership in the function
    account.balance = 10;
    account
//  ^^^^^^^ return the ownership back
}

fn main() {
    let mut account = Account::new(1, String::from("Alice"));

    account = change_account(account);
//  ^^^^^^^^^^               ^^^^^^^ move the ownership to the function
//  get back the ownership

    println!("{:#?}", account);
}
```
This way we are moving the ownership forth and back. This is not recommended because it is not efficient and very tedious.


## Option 2: use mutable reference

We can use mutable reference to achieve the same result without moving the ownership forth and back, which is more elegant and efficient.

```rust
fn change_account(account: &mut Account) {
  //                       ^^^^^^^^^^^^ take a mutable reference, no ownership is moved
    account.balance = 10;

  // no need to return ownership back
}

fn main() {
    let mut account = Account::new(1, String::from("Alice"));

    change_account(&mut account);
    //             ^^^^^^^^^^^^ create a mutable reference and pass it to the function
    // we don't need to get back the ownership
    // because we are not moving the ownership

    println!("{:#?}", account);
}

```

But to prevent unexpected updates, we need to follow [[2024-11-09_Analyze-rule-5-and-6-from-twelve-rules|rule 5 and 6 from twelve rules]] when using mutable reference to update a value.
