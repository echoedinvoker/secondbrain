---
date: 2024-11-10
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create total_balance and summary methods to bank instance

In the examples below, the methods we need to add are instance methods that do not require additional arguments, similar to the [[2024-11-10_Create-summary-method-of-account-instance|previous case]]. However, here we need to use for loop in the methods to handle all the accounts.

## Requirements

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

    fn summary(&self) -> String {
        format!("{} has balance: {}", self.holder, self.balance)
    }

    fn deposit(&mut self, amount: i32) -> i32 {
        self.balance += amount;
        self.balance
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

    fn add_account(&mut self, account: Account) {
        self.accounts.push(account);
    }

    // create a method to return the total balance of all accounts

    // create a method to return the summary of all accounts
}

fn main() {
    let mut bank = Bank::new();
    let mut account1 = Account::new(1, "Alice".to_string());
    account1.deposit(100);
    bank.add_account(account1);

    let mut account2 = Account::new(2, "Bob".to_string());
    account2.deposit(50);
    bank.add_account(account2);

}
```

## Solution by for loop in the method

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

    fn summary(&self) -> String {
        format!("{} has balance: {}", self.holder, self.balance)
    }

    fn deposit(&mut self, amount: i32) -> i32 {
        self.balance += amount;
        self.balance
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

    fn add_account(&mut self, account: Account) {
        self.accounts.push(account);
    }

    // create a method to return the total balance of all accounts
    fn total_balance(&self) -> i32 {
        let mut total = 0;
        for account in &self.accounts {
        //  ^^^^^^^ type is &Account, this is an important feature of Rust
        //          it prevents the ownership move in each iteration
        //          when the source is a reference

            total += account.balance;
        }
        total
    }

    // create a method to return the summary of all accounts
    fn summary(&self) -> Vec<String> {
        let mut summaries = vec![];
        for account in &self.accounts {
        //  ^^^^^^^ type is &Account
            summaries.push(account.summary());
        }
        summaries
    }
}

fn main() {
    let mut bank = Bank::new();
    let mut account1 = Account::new(1, "Alice".to_string());
    account1.deposit(100);
    bank.add_account(account1);

    let mut account2 = Account::new(2, "Bob".to_string());
    account2.deposit(50);
    bank.add_account(account2);

    // use the methods
    println!("{:#?}", bank.summary());
    // ["Alice has balance: 100", "Bob has balance: 50"]
    println!("{}", bank.total_balance());
    // 150
}
```

## Further simplification

We can further simplify for loop codes by using the `iter` method of vectors. [[2024-11-10_Use-iter-method-of-vectors-to-simplize-codes|here]] is the examples.
