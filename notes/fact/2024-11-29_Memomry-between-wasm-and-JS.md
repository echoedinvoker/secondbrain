---
date: 2024-11-29
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Memomry between wasm and JS


## What is memory?

As long as we assign a value to a variable, the value will also be saved in memory. Whether we notice it or not, memory allows us to access this value later on, and delete unnecessary values as the program progresses.


## Memory between wasm and JS

The memories on both sides are independent, but:
- we can either create memory in WebAssembly and export it to JavaScript. [[2024-11-29_create-memory-in-WebAssembly-and-export-it-to-JavaScript|example]]
- we can create memory in JavaScript and import it into WebAssembly.

