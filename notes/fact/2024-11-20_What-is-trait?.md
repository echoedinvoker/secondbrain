---
date: 2024-11-20
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# What is trait?

A **trait** is a set of methods. It can contain **abstract methods** and **default methods**.

```rust
trait Vehicle {
    // abstract method
    fn start(&self);  // only signature, no body

    // default method
    fn stop(&self) {
        println!("Vehicle stopped");  // has default implementation inside { }
    }
}
```

We can implement the trait for a struct, enum, or even some primitive types.

```rust
struct Car {};

trait Vehicle {
    fn start(&self);
    fn stop(&self) {
        println!("Vehicle stopped");
    }
}

// implement trait `Vehicle` for struct `Car`
impl Vehicle for Car {

    // need to write abstract method `start` which defined in trait `Vehicle`
    fn start(&self) {
        println!("Car started");
    }

    // we can override default method `stop` if we want, or just use the default implementation
}

```

Now, we can create a `Car` object and it has `start` and `stop` methods.
