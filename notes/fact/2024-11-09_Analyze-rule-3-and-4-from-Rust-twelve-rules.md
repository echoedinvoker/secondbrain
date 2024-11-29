---
date: 2024-11-09
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Analyze rule 3 and 4 from Rust twelve rules

We look details of rule 3 and 4 from [[2024-11-07_Twelve-rules-of-writing-Rust-script|Twelve rules of writing Rust script]]: 

## Rule 3: You can create many read-only references to a value that exist at the same time

We already know that we can use [[2024-11-09_&-operator|&]] to create a reference to a value.

>It make borrowed value READ-ONLY by default! During the reference's lifetime.

It match fix 1 of [[2024-11-08_Fix-JS-issue-from-reference-to-same-object|concept to avoid bugs]]. So we can create **MULTIPLE** references to the same value at the same time.

```rust
fn print_account(account: &Account) {
    println!("{:#?}", account);
}

fn main() {
    let account = Account::new(1, String::from("Alice"));

    let account_ref1 = &account;
    let account_ref2 = &account;
//  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ we can create multiple READ-ONLY references to the same value

    print_account(account_ref1);
    print_account(account_ref2);

    println!("{:#?}", account);
}

```

## 4. You can't move a value while a ref to the value exists

This rule effectively prevents unexpected changes from occurring when the reference is being used.

```rust
fn main() {
    let account = Account::new(1, String::from("Alice"));

    let account_ref1 = &account;
    let account_ref2 = &account;

    let account_moved = account; // ERROR: value moved here
                                 // during value is borrowed, we can't move the value

    print_account(account_ref1);
    print_account(account_ref2);

    println!("{:#?}", account);
}
```

We visualize the codes above as follows:

![rule-3-error.png](../assets/imgs/rule-3-error.png)


