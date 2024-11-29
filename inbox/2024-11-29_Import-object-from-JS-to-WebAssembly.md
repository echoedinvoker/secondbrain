---
date: 2024-11-29
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Import object from JS to WebAssembly

We can import objects from JavaScript to WebAssembly. We continue with the [[2024-11-28_Initiate-wasm-from-wasm-file|previous example]] and add an object into wasm module and use it in the wasm module:

```js
// index.js

async function init() {

  // create an object to import
  const importObject = {
    console: {
      log: () => console.log("Hello from WebAssembly!"),  // function value is allowed
      error: () => console.error("Error from WebAssembly!")
    }
  }

  const response = await fetch("sum.wasm");
  const buffer = await response.arrayBuffer()
  const wasm = await WebAssembly.instantiate(buffer, importObject);
                                                //   ^^^^^^^^^^^^ pass the object to the instantiate function (2nd argument)
  const sumFunction = wasm.instance.exports.sum;
  const result = sumFunction(100, 1000);
  console.log(result);
}

init();

```

```wasm
(module
  (import "console" "log" (func $log))  // use import node to import the specific value of the object (so we don't import the whole object at once)
  (import "console" "error" (func $error))  // "" is to select the specific value in the object, $ is the name you prefer to use in the wasm module
  (func $sum (param $a i32) (param $b i32) (result i32)

    call $log  // use `call` to use the imported function value
    call $error

    local.get $a
    local.get $b
    i32.add
  )
  (export "sum" (func $sum))
)

```

Same as [[2024-11-27_Download-binary-.wasm-file-from-wat2wasm-website|download binary .wasm file from wat2wasm website]], we got the `sum.wasm` and put it into the webpack output directory.

Then run the webpack dev server:
```bash
npm run dev

# We should see the output on the browser console:
# Hello from WebAssembly!
# Error from WebAssembly!
# 1100

