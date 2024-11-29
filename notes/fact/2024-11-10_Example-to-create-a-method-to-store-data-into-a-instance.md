---
date: 2024-11-10
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Example to create a method to store data into a instance

```rust
#[derive(Debug)]
struct Account {
    id: u32,
    balance: i32,
    holder: String,
}

impl Account {
    fn new(id: u32, holder: String) -> Self {
        Account {
            id,
            holder,
            balance: 0,
        }
    }
}

#[derive(Debug)]
struct Bank {
    accounts: Vec<Account>,
}

impl Bank {
    fn new() -> Self {
        Bank { accounts: vec![] }
    }
}

fn main() {
    let mut bank = Bank::new();
    let account = Account::new(1, "Alice".to_string());
}
```

The requirement is to add a method `add_account` for `Bank`, which can store the `Account` value in the `Bank`'s `accounts` field.

According to [[2024-11-10_Analyze-rule-11-of-twelve-rules|rule 11]], we know that the method's argument should be passed by value in this requirement, so we can write as follows:

```rust
#[derive(Debug)]
struct Account {
    id: u32,
    balance: i32,
    holder: String,
}

impl Account {
    fn new(id: u32, holder: String) -> Self {
        Account {
            id,
            holder,
            balance: 0,
        }
    }
}

#[derive(Debug)]
struct Bank {
    accounts: Vec<Account>,
}

impl Bank {
    fn new() -> Self {
        Bank { accounts: vec![] }
    }

    // Add the `add_account` method
    fn add_account(&mut self, account: Account) {
    //             ^^^^                ^^^^^^^ pass the `account` value by ownership moving
    //             because we need to modify the `Bank` instance, we need to make it mutable
      
      self.accounts.push(account); // store the `account` value into the `Bank` instance
    }
}

fn main() {
    let mut bank = Bank::new();
    //  ^^^ because we need to modify the `bank` value, we need to make it mutable
    let account = Account::new(1, "Alice".to_string());

    // Call the `add_account` method to store the `account` value
    bank.add_account(account);
    //               ^^^^^^^ ownership moved

    println!("{:?}", bank);
    // Bank { accounts: [Account { id: 1, balance: 0 }] }
}
```
