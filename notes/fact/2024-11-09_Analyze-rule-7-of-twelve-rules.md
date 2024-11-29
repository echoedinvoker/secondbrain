---
date: 2024-11-09
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Analyze rule 7 of twelve rules

Sometimes we may find that some codes seem to break the ownership system, and this is actually the reason for rule 7 of [[2024-11-07_Twelve-rules-of-writing-Rust-script|twelve rules of writing Rust script]]:


7. Some types of values are copied instead of moved (numbers, bools, chars, arrays/tuples with copyable elements)


Below we accurately list the all types that comply with rule 7:

Types that are copied:
- **All numbers** (Examples: i32, u32, f32)
- **bool** (true/false)
- **char** (single characters)
- **Tuples** (if everything inside is Copy-able)
- **Arrays** (if everything inside is Copy-able), but NOT *vectors*
- **References** (both readable and writable)

*This means they behave more like values in other languages*


```rust
fn main() {
    let account = Account::new(1, String::from("Alice"));

    let other_account = account;  // ownership transfer to other_account from account

    println!("{:#?}", account);
    //                ^^^^^^^ ERROR: account value already moved
}
```

Above is an example of the ownership system, where if we replace the account with a simple number as follows:

```rust
fn main() {
    let number = 1;

    let other_number = number; // there is no any ownership transfer, just a copy of value
                               // to binding other_number

    println!("{}, {}", number, other_number);  // no any error, works properly
}
```

We visualize the codes above as follows:

![copy-value.png](../assets/imgs/copy-value.png)
