---
date: 2024-11-27
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Write the first WASM code

We can write our frist WASM code in [https://webassembly.github.io/wabt/demo/wat2wasm/], this website can convert our WASM code to binary format and check if it is correct or not.

```html
(module
  (func $sum (param $a i32) (param $b i32) (result i32)
    <!--^ any name is start with $                 ^^^ WASM need to specify the type of the param and result -->
    <!--                                               but only four types: i32, i64, f32, f64 -->
    <!--                                               other types are represented by something clalled glue code -->

    <!-- below codes are return the sum of $a and $b -->
    local.get $a
    local.get $b
    i32.add
  )
)

```


Next, we can export the function and use it in JS, see [[2024-11-27_Export-our-first-wasm-fn-and-use-it-in-JS|this topic]] for more details. 
