---
date: 2024-11-08
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Using borrow system to solve problem from ownership system

This is [[2024-11-08_Given-the-ownership-system,-how-do-we-write-useful-code?|problems from Ownership system]].

```rust
fn print_account(account: Account) {
    println!("{:#?}", account);
}

fn main() {
    let account = Account::new(1, String::from("Alice"));

    print_account(account);

    println!("{:#?}", account);  // Error: value of account already moved
}

```
Here we can use borrow system to solve this problem.

```rust
fn print_account(account: &Account) {
//                        ^ use & to pass reference of Account value
//                          instead of passing Account value into function
    
    println!("{:#?}", account);
    //                ^^^^^^^ `account` here is reference of Account value
    //                        It will use it to get value of Account to print automatically
}

fn main() {
    let account = Account::new(1, String::from("Alice"));

    let account_ref = &account;
//  ^^^^^^^^^^^^^^^^^^^^^^^^^^^ create reference of Account value
//                              and bind it to a binding `account_ref``

    print_account(account_ref);
//                ^^^^^^^^^^^ pass reference of Account value into function

    println!("{:#?}", account);
//                    ^^^^^^^ we can still use `account` here because
//                            we didn't pass `account` value into function
//                            we just passed reference of `account` value into function

    print_account(account_ref);
//                ^^^^^^^^^^^ we can still use `account_ref` here because
//                            reference doesn't follow ownership rules
}

```

We visualize the process of the codes above as follows:

![borrow-ref.png](../assets/imgs/borrow-ref.png)


It can be found that references do not need to follow the rules of ownership, so when we pass a reference to a function, it actually copies the reference over, so the reference in the binding `account_ref` still exists.
