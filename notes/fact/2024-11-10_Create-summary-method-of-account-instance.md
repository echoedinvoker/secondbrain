---
date: 2024-11-10
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create summary method of account instance


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

    // create a summary method that returns a string
    // to describe who is the holder and the balance he has
}


fn main() {
    let mut account = Account::new(1, "Alice".to_string());
}

```

## Design arguments type of summary method

The summary method does not require any additional arguments, but it is an instance method, so it will still have a parameter `&self` at the first position.

But we still can use [[2024-11-10_Analyze-rule-11-of-twelve-rules|rule 11]] to analyze `&self` parameter.

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

    // create a summary method that returns a string
    // to describe who is the holder and the balance he has
    fn summary(&self) -> String {
    //         ^^^^^ we still can use rule 11 to analyze it
    //               pass read-only reference to self, means we only use the self instance
    //               to do some calculation, no ownership transfer or mutation

      format!("Account holder: {}, balance: {}", self.holder, self.balance)  // return a string
    //                                           ^^^^^^^^^^^  ^^^^^^^^^^^^
    //                                           read-only refs, no ownership transfer or mutation
    }
}


fn main() {
    let account = Account::new(1, "Alice".to_string());

    println!("{}", account.summary());
    // output: Account holder: Alice, balance: 0

    println!("{:#?}", account); // works because method summary didn't do any ownership transfer or mutation
    // output: Account { id: 1, balance: 0, holder: "Alice" }

}
```

[[2024-11-10_Create-total_balance-and-summary-methods-to-bank-instance|here]] are more examples which combine with for loop technique.
```
