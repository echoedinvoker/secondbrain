---
date: 2024-11-14
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Three area of computer memory

There are three main areas of computer memory. They are:
- Stack
- Heap
- Data segment, Rodata(read-only data) segment, and Static segment


## qualities of each area

**Stack**
Fast, but limited in size (2-8MB)

**Heap**
Slow, but can grow to store a large amount of data

**Data segment, Rodata(read-only data) segment, and Static segment**
Store literal values that we write into our code
(e.g. `int x = 5;` stores `5` in the data segment)


## The most common distribution pattern

According to the characteristics of each area, we usually put the metadata of the data on the stack, while the data itself is placed on the heap. Here are some examples:

### common example

```rust
let nums = vec![1, 2, 3];
```
**Stack**
```rust
Vec {
  ptr: 0x12345678,  // pointer to the heap address
  len: 3,
  capacity: 5,
}
```
**Heap**
```rust
[1, 2, 3, _, _] // 0x12345678
```

**Data segment, Rodata(read-only data) segment, and Static segment**
```rust
[1, 2, 3] // init value we write in our code will be stored here,
          // and heap will copy it to its memory space
```

### nested data structure example

In this case, the heap will also store the metadata of the nested data structure.
```rust
let nums = vec![vec![1, 2], vec![3, 4]];
```
**Stack**
```rust
Vec {
  ptr: 0x12345678,  // pointer to the heap address
  len: 2,
  capacity: 5,
}
```
**Heap**
```rust
[ // 0x12345678
  Vec {  // nested Vec metadata be stored in heap instead of stack
    ptr: 0x87654321,  
    len: 2,
    capacity: 2,
  },
  Vec {
    ptr: 0x98765432,
    len: 2,
    capacity: 2,
  },
]

[1, 2] // 0x87654321
[3, 4] // 0x98765432
```

**Data segment, Rodata(read-only data) segment, and Static segment**
```rust
[1, 2] // init value we write in our code will be stored here,
       // and heap will copy it to its memory space
[3, 4] // init value we write in our code will be stored here,
       // and heap will copy it to its memory space
```
