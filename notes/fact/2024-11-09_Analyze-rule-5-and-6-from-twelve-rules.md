---
date: 2024-11-09
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Analyze rule 5 and 6 from twelve rules

From [[2024-11-07_Twelve-rules-of-writing-Rust-script|twelve rules of writing Rust script]], the rules 5 and 6 are:

5. You can make a writable (mutable) reference to a value only if there are no read-only references currently in use. One mutable ref to a value can exist at a time

6. You can't mutate a value through the owner when any ref (mutable or immutable) to the value exists

These two rules are meant to prevent unexpected updates when we using [[2024-11-09_Mutating-value-in-the-function|mutable reference]] to update a value. 


## Example to break rule 5.1 - using mutable reference when immutable reference exists

```rust
fn change_account(account: &mut Account) {
    account.balance = 10;
}

fn main() {
    let mut account = Account::new(1, String::from("Alice"));

    let account_ref = &account;
    //                ^^^^^^^^ create a immutable reference

    change_account(&mut account);
    //             ^^^^^^^^^^^^ ERROR: cannot create mutable reference
    //                          when immutable reference exists

    println!("{:#?}", account_ref);
    //                ^^^^^^^^^^^ use immutable reference
}
```

## Example to break rule 5.2 - create multiple mutable references at the same time

```rust
fn change_account(account: &mut Account) {
    account.balance = 10;
}

fn main() {
    let mut account = Account::new(1, String::from("Alice"));

    let account_ref = &mut account;
    //                ^^^^^^^^ create a mutable reference

    change_account(&mut account);
    //             ^^^^^^^^^^^^ ERROR: cannot create mutable reference
    //                          when other mutable reference exists

    println!("{:#?}", account_ref);
    //                ^^^^^^^^^^^ use mutable reference
}
```
So, simply put, when you have a mutable reference pointing to a value, you cannot have any other reference pointing to this value, whether it is a mutable reference or an immutable reference.


## Example to break rule 6 - using mutable reference when immutable reference exists

```rust
fn main() {
    let mut account = Account::new(1, String::from("Alice"));

    account.balance = 10;
//  ^^^^^^^^^^^^^^^^^^^^^ this is totally fine, we can mutate the value
//                        where the ownership is
}
```
```rust
fn main() {
    let mut account = Account::new(1, String::from("Alice"));

    let account_mut_ref = &mut account;
    //                    ^^^^^^^^^^^^ create a mutable reference

    account.balance = 10; // ERROR: cannot mutate the value during the mutable reference exists
                          // even the ownership is here 

    change_account(account_mut_ref);
    //             ^^^^^^^^^^^^^^^ use mutable reference
}
```

You may feel these rules strange, but the starting point is to avoid any unexpected updates.
