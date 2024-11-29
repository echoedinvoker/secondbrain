---
date: 2024-11-05
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create new RUST project and run it

```bash

cargo new deck  # create new project name `deck`

cd deck

ls   # Cargo.toml  src/

cargo run  # compile and run the project

   Compiling deck v0.1.0 (/home/mattc/Documents/gith/std/rust/deck)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.50s
     Running `target/debug/deck`
Hello, world! # this is real output, above is some debug info

cargo run -q
#         ^^ means quiet, no debug info

Hello, world! # only output

```
