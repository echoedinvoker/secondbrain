---
date: 2024-11-27
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Export our first wasm fn and use it in JS

In [[2024-11-27_Write-the-first-WASM-code|this topic]], we wrote our first wasm code. Now, we will export the fn and use it in JS:

```html
(module
  (func $sum (param $a i32) (param $b i32) (result i32)
    local.get $a
    local.get $b
    i32.add
  )

  <!-- add this node to export above fn `sum` -->
  (export "whatever" (func $sum))
      <!-- ^^^^^^^^ export name, we use it in JS -->
)
```

```js
const wasmInstance = new WebAssembly.Instance(wasmModule, {});
const { whatever } = wasmInstance.exports;
//      ^^^^^^^^ export name of wasm code
for (let i = 0; i < 10; i++) {
  console.log(whatever(i, i));
  //          ^^^^^^^^ then, we can use it as normal fn in JS
}
```
