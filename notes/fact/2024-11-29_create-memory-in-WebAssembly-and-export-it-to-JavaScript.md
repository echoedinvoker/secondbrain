---
date: 2024-11-29
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# create memory in WebAssembly and export it to JavaScript

We continue codes of [[2024-11-29_Import-object-from-JS-to-WebAssembly|this topic]], create memory in WebAssembly and export it to JavaScript.

```wasm
(module
  (import "console" "log" (func $log))
  (import "console" "error" (func $error))

  (memory 1)  // create 1 page memory, 1 page is about 64KB
  //      ^ this is number of page

  (func $sum (param $a i32) (param $b i32) (result i32)
    call $log
    call $error
    local.get $a
    local.get $b
    i32.add
  )

  (export "mem" (memory 0))  // export memory of index 0 to JavaScript as name "mem"
  //                    ^ this is index, don't confuse with the page number above

  (export "sum" (func $sum))
)
```

If you find (memory 1) (memory 0) easy to confuse, we can also give created memory a name in wasm so that it won't be confused.

```wasm
(module
  (import "console" "log" (func $log))
  (import "console" "error" (func $error))

  (memory $mem 1)
  //      ^^^^ this is name of memory in wasm

  (func $sum (param $a i32) (param $b i32) (result i32)
    call $log
    call $error
    local.get $a
    local.get $b
    i32.add
  )

  (export "mem" (memory $mem))
  //                    ^^^^ becasue we give memory a name, we can use it instead of index
  

  (export "sum" (func $sum))
)

```

Then, we can directly store some value in the memory.

```wasm
(module
  (import "console" "log" (func $log))
  (import "console" "error" (func $error))
  (memory $mem 1)

  (data (i32.const 0) "Hi")  // store "Hi" at the beginning of memory
  //    ^^^^^^^^^^^^^ this means the store place is start from the first byte of memory

  (func $sum (param $a i32) (param $b i32) (result i32)
    call $log
    call $error
    local.get $a
    local.get $b
    i32.add
  )
  (export "mem" (memory $mem))  // now the exported memory has "Hi" stored in it
  (export "sum" (func $sum))
)
```

By [[2024-11-27_Download-binary-.wasm-file-from-wat2wasm-website|this way]], we refresh the `sum.wasm` file, then we can try to get `Hi` in JavaScript.

```js
async function init() {

  const importObject = {
    console: {
      log: () => console.log("Hello from WebAssembly!"),
      error: () => console.error("Error from WebAssembly!")
    }
  }

  const response = await fetch("sum.wasm");
  const buffer = await response.arrayBuffer()
  const wasm = await WebAssembly.instantiate(buffer, importObject);
  const sumFunction = wasm.instance.exports.sum;

  const wasmMemory = wasm.instance.exports.mem;  // get exported memory from wasm
                                      //  ^^^^ exported memory name defined in wasm

  debugger
}

init();

```

![devtool-mem.png](../assets/imgs/devtool-mem.png)


```js
async function init() {

  const importObject = {
    console: {
      log: () => console.log("Hello from WebAssembly!"),
      error: () => console.error("Error from WebAssembly!")
    }
  }

  const response = await fetch("sum.wasm");
  const buffer = await response.arrayBuffer()
  const wasm = await WebAssembly.instantiate(buffer, importObject);
  const sumFunction = wasm.instance.exports.sum;
  const wasmMemory = wasm.instance.exports.mem;

  const uint8Array = new Uint8Array(wasmMemory.buffer, 0, 12); // only first 2 bytes store "Hi"
                                                               // so we only get first 2 bytes

  const hiText = new TextDecoder().decode(unit8Array); // decode the bytes to string

  console.log(hiText); // print the string `Hi` on console
}

init();

```

```bash
npm run dev

# we should see `Hi` on console
```
