---
date: 2024-11-12
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Use enum Option to indicate the presence or absence of struct field

[[2024-11-12_Rust-built-in-Option-enum,-taking-care-of-may-or-may-not-getting-thing-issue|Enum Option]] is not only used to handle the case where you may or may not get a value to return, but also to indicate the presence or absence of a field in a struct.

```rust
#[derive(Debug)]
struct Employee {
    name: String,
}
 
#[derive(Debug)]
struct Task {
    assigned_to: Option<Employee>,
    //           ^^^^^^^^^^^^^^^^ Here we use Option<Employee> to indicate
    //                            that the field assigned_to may or may not have a Employee value.
}
 
fn main() {
    let employee1 = Employee {
        name: String::from("me"),
    };
 
    let task1 = Task { assigned_to: None };
    //                              ^^^^ Here we use None to indicate that
    //                                   the field `assigned_to` does not have a value.
 
    let task2 = Task { assigned_to: Some(employee1) };
    //                              ^^^^^^^^^^^^^^^ because we define `assigned_to` as an Option<Employee>,
    //                                              we should wrap the Employee value in Some.
}
```

