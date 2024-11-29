---
date: 2024-11-07
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Types of number in Rust

| Types | Description | Range |
|-------|-------------|--------|
| i8 | Positive or negative integers | -128 to 127 |
| i16 | Positive or negative integers | -32,768 to 32,767 |
| i32 | Positive or negative integers | -2,147,483,648 to 2,147,483,647 |
| i64 | Positive or negative integers | -9.2E18 to 9.2E18 |
| i128 | Positive or negative integers | -1.7E38 to 1.7E38 |
| isize | Positive or negative integers | -9.2E18 to 9.2E18 |
| u8 | Unsigned integers | 0 to 255 |
| u16 | Unsigned integers | 0 to 65,535 |
| u32 | Unsigned integers | 0 to 4.29E9 |
| u64 | Unsigned integers | 0 to 1.84E19 |
| u128 | Unsigned integers | 0 to 3.4E38 |
| usize | Unsigned integers | 0 to 1.84E19 |
| f32 | Decimal values | -3.4E38 to 3.4E38 |
| f64 | Decimal values | -1.7E308 to 1.7E308 |


i = integer (positive or negative, no decimal)
u = unsigned integer (only positive, no decimal)
f = float (decimal, positive or negative)
8, 16, 32, 64, 128 = size in bits, which also determines the range of values it can store

`isize` and `usize` are used for indexing collections or indicating size and capacity, and their size depends on the architecture of the system (32-bit or 64-bit), making them more flexible for such purposes.
