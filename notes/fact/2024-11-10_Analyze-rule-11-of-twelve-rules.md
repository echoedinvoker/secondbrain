---
date: 2024-11-10
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Analyze rule 11 of twelve rules

## Rule 11

From [[2024-11-07_Twelve-rules-of-writing-Rust-script|twelve rules of writing Rust script]], rule 11 is:

11. These rules will dramatically change how you write code (compared to other languages)


## Thinking when we write Rust function or data structure

Because of the existence of the Ownership system and Borrowing system, whenever we design a function or data structure, we need to consider:

- With every function we write, we need to think about whether we are **receiving values or refs!**

- With every data structure we define, we need to think about whether we are **storing values or refs!**


## General guidelines for designing function argument types (not concrete rules)

- Need to store the argument somewhere?  ---> **Favor taking ownership (receive value)**

[[2024-11-10_Example-to-create-a-method-to-store-data-into-a-instance|here]] is an example to create a method to store data into a instance.


- Need to do a calculation on the argument?  ---> **Favor receiving a read-only ref**


- Need to change the value in some way?  ---> **Favor receiving a mutable ref**


## Above guidelines are not concrete rules, below are some exception cases

[[2024-11-10_Create-deposit-and-withdraw-methods-to-account-instance|Create deposit and withdraw methods to account instance]]
