---
date: 2024-11-28
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Initiate wasm from wasm file

In [[2024-11-28_Use-Webpack-to-serve-init-index.html|this topic]], the bytes array is hard coded in the JavaScript file. We want to replace it with a .wasm file.

Follow [[2024-11-27_Download-binary-.wasm-file-from-wat2wasm-website|this topic]] to download the .wasm file from wat2wasm website and put it in the webpack output path (usually `public` folder):

```bash
~/D/g/s/w/init > tree public/  # assume the webpack output path is `public`
public/
└── sum.wasm  # put the .wasm file here
```


```js
// index.js

async function init() {

  // replace the hard coded bytes array with fetching the .wasm file
  const response = await fetch("sum.wasm");  // here I choose to use native fetch API to fetch the .wasm file
                            //  ^^^^^^^^
  const buffer = await response.arrayBuffer()
                          //  ^^^^^^^^^^^^^^ to get buffer just like the hard coded bytes array before

  const wasm = await WebAssembly.instantiate(buffer);
                                        //   ^^^^^^ same as before, but this time the buffer is from the .wasm file
                                          
  const sumFunction = wasm.instance.exports.sum;
  const result = sumFunction(100, 1000);
  console.log(result);
}

init();

```

```bash
npm run dev   # we should see the same result in the console of the browser
```
