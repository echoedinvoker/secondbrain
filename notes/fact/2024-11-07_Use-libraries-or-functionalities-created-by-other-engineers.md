---
date: 2024-11-07
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Use libraries or functionalities created by other engineers

If there is such a need, in other languages we can include `packages` to achieve this goal, in Rust we can include `crates`, which is actually equivalent to packages.

There are two types of crates:


**Rust Standard Library**

This is the standard library that comes with Rust. It is included in the Rust installation and does not need to be included in the `Cargo.toml` file.

You can check its documentation [here](https://doc.rust-lang.org/std/).


**External Crates**

External crates are libraries that are not included in the standard library. They need to be included in the `Cargo.toml` file, which means that you need to install them before using them.

You can find external crates in the [crates.io](https://crates.io/) website.

You can find the documentation of the crates in the [docs.rs](https://docs.rs/) website.

Bellow is an example of how to include an external crate in your project:

```bash
cargo add rand  # use command `cargo add` to install a crate `rand`

```


```toml
# Cargo.toml 

[package]
name = "deck"
version = "0.1.0"
edition = "2021"

[dependencies]
rand = "0.8.5"  # this line is added by the command `cargo add rand` automatically

```
