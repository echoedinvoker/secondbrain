---
date: 2024-11-10
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Use iter method of vectors to simplize codes


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

    fn total_balance(&self) -> i32 {

        // try to simplify the codes below by iter method of vectors
        let mut total = 0;
        for account in &self.accounts {
            total += account.balance;
        }
        total
    }

    fn summary(&self) -> Vec<String> {

        // try to simplify the codes below by iter method of vectors
        let mut summaries = vec![];
        for account in &self.accounts {
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


## Simplified codes by iter method of vectors

Above codes come from [[2024-11-10_Create-total_balance-and-summary-methods-to-bank-instance|this topic]], and we can use `iter` method of vectors to simplify the codes.

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

    fn total_balance(&self) -> i32 {

        // below we use iter method of vectors to simplify the codes
        // and it is only one line of expression, so it can be returned directly
        self.accounts
            .iter()
            .map(|account| account.balance)
            .sum()
    }

    fn summary(&self) -> Vec<String> {

        // below we use iter method of vectors to simplify the codes
        // and it is only one line of expression, so it can be returned directly
        self.accounts
            .iter()
            .map(|account| account.summary())
            .collect()
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

    println!("{:#?}", bank.summary());
    println!("{}", bank.total_balance());
}
```
