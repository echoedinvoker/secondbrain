---
date: 2024-11-06
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Inherent implement

`Inherent implement` is used to implement `associate function` or `method` to struct.

`associate function` is equivalent to `static method` or `class method` in other languages.

Bellow is the example of defining `associate function` and `method` to `struct` by using `inherent implement`:

```rust
    struct Circle {
        radius: f64,
    }

    impl Circle {
//  ^^^^^^^^^^^ `impl` is a keyword used to define inherent implementation
//              `Circle` is the struct name to which we are implementing the methods or associate functions

        fn new(radius: f64) -> Self {
        //     ^^^^^^^^^^^  `new` is a associate function because first parameter is not `self`
            Circle { radius }
        }

        fn area(&self) -> f64 {
        //      ^^^^^^ `area` is a method because first parameter is `self`
            std::f64::consts::PI * self.radius * self.radius
        }

    }
```

Then we can use the `associate function` and `method` as below:

```rust
    fn main() {
        let c = Circle::new(2.0);
        //      ^^^^^^^^^^^^^^^^^ because associate function is binded to struct,
        //                        so we should call it with struct name and `::` operator

        println!("Area of circle is {}", c.area());
        //                               ^^^^^^^^ methods are binded to instances, so we should call them with instance name and `.` operator
    }
```


About `associate function`, it is common to use `new` as a name of `associate function` to create new instance of struct. [[2024-11-06_Refactor-building-new-instance-codes-by-inherent-implement|Here]] is an example.
