---
date: 2024-11-10
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create deposit and withdraw methods to account instance


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

    // create deposit method

    // create withdraw method

}

fn main() {
    let account = Account::new(1, "Alice".to_string());
}

```

## Rule 11

From [[2024-11-10_Analyze-rule-11-of-twelve-rules|rule 11]], we have three options to design arguments of  the deposit and withdraw methods:

1. receive the amount as a value (ownership moved)

This option is not suitable because we don't want to store the amount in the account instance.

2. receive the amount as a read-only reference

We just need to use the amount to calculate the new balance, so this option is suitable.

3. receive the amount as a mutable reference

This option is not suitable because we don't need to mutate the amount.


## Rule 7

Based on the analysis of rule 11 from above, we should pass amount as a read-only reference to the deposit and withdraw methods.

However, amount is of type i32. According to [[2024-11-09_Analyze-rule-7-of-twelve-rules|rule 7]], when we pass amount as value, we actually make a copy of amount, which is no different from passing amount as a read-only reference.

Finally, we decide to pass(copy) amount as a value for more simplicity syntax as below:

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

    // create deposit method
    fn deposit(&mut self, amount: i32) -> i32 {
//                        ^^^^^^^^^^^  ^^^^^^ return value type is i32, so we return a copy of it
//                        amount is passed as a value, which is a copy of amount
      self.balance += amount;
      self.balance
//    ^^^^^^^^^^^^ balance field type is i32, so we return a copy of it
//                 no ownership moved here
    }

    // create withdraw method
    fn withdraw(&mut self, amount: i32) -> i32 {
      self.balance -= amount;
      self.balance
    }

}

fn main() {
    let mut account = Account::new(1, "Alice".to_string());
//      ^^^ deposit and withdraw methods are called to mutant the account instance
    println!("{:?}", account.deposit(100));
    println!("{:?}", account.withdraw(50));
    println!("{:#?}", account);
}
```

