---
date: 2024-11-29
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Create memory in JS and used it in wasm

In [[2024-11-29_create-memory-in-WebAssembly-and-export-it-to-JavaScript|this topic]], we create memory in wasm, store data in it and export it to JS. In this topic, we will create memory in JS and use it in wasm:

```js
// index.js

async function init() {

  const memory = new WebAssembly.Memory({ initial: 1 }); // instead in wasm, we create memory in JS
  //                 ^^^^^^^^^^^^^^^^^^   ^^^^^^^^^^ start with 1 page memory size
  //                  ^ this memory is for wasm use, so we should create it with WebAssembly.Memory

  const importObject = {

    // pass memory to wasm, just like to pass value to wasm
    js: {
      mem: memory
    },

    console: {
      log: () => console.log("Hello from WebAssembly!"),
      error: () => console.error("Error from WebAssembly!")
    }
  }

  const response = await fetch("sum.wasm");
  const buffer = await response.arrayBuffer()
  await WebAssembly.instantiate(buffer, importObject);
                                //      ^^^^^^^^^^^^ same as functions, memory can be imported to wasm here by WebAssembly.instantiate

  const uint8Array = new Uint8Array(memory.buffer, 0, 12);
  //                                ^^^^^^^^^^^^^ we can direct use memory because it is created in JS, no need to export it from wasm

  const hiText = new TextDecoder().decode(uint8Array);

  console.log(hiText);
}

init();

```


```wasm
(module
  (import "console" "log" (func $log))
  (import "console" "error" (func $error))

  (memory (import "js" "mem") 1)
  //      ^^^^^^^^^^^^^^^^^^^ instead of creating memory, we import it from JS
  //                          "js" "mem" is to specify the place where memory is in the imported object

  (data (i32.const 0) "Hi")  // store data in memory, no difference with the memory created in wasm

  (func $sum (param $a i32) (param $b i32) (result i32)
    call $log
    call $error
    local.get $a
    local.get $b
    i32.add
  )

  // we don't need to export memory because it is created in JS, JS can direct use it

  (export "sum" (func $sum))
)

```

```bash
npm run dev
# you will see "Hi" in the console of the browser
```
