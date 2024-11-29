---
date: 2024-11-28
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Use Webpack to serve init index.html

In [[2024-11-28_Initiate-wasm-from-bytes-array|this topic]], we init WebAssembly from a bytes array but we need to open `index.html` manually on the browser. We can use Webpack to serve the `index.html` automatically by following these steps:

```bash
~/D/g/s/w/init > tree
.
├── public
│   └── index.html  # create folder `public` and put `index.html` inside
│
└── index.js  # create this file and move script codes from `index.html` to here
```

So now the contents of index.html and index.js are as follows:

```html
<!-- public/index.html -->

<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <h1>Hello World</h1>
  <!-- script is removed -->
</body>

</html>

```

```js
// index.js

// codes from script tag in index.html
async function init() {
  const byteArray = new Int8Array([0x00, 0x61, 0x73, 0x6d, 0x01, 0x00, 0x00, 0x00, 0x01, 0x07, 0x01, 0x60, 0x02, 0x7f, 0x7f, 0x01, 0x7f, 0x03, 0x02, 0x01, 0x00, 0x07, 0x07, 0x01, 0x03, 0x73, 0x75, 0x6d, 0x00, 0x00, 0x0a, 0x09, 0x01, 0x07, 0x00, 0x20, 0x00, 0x20, 0x01, 0x6a, 0x0b, 0x00, 0x18, 0x04, 0x6e, 0x61, 0x6d, 0x65, 0x01, 0x06, 0x01, 0x00, 0x03, 0x73, 0x75, 0x6d, 0x02, 0x09, 0x01, 0x00, 0x02, 0x00, 0x01, 0x61, 0x01, 0x01, 0x62]);
  const wasm = await WebAssembly.instantiate(byteArray.buffer);
  const sumFunction = wasm.instance.exports.sum;
  const result = sumFunction(100, 1000);
  console.log(result);
}

init();
```

Then, we need to init a new project and install `webpack` things:

```bash
~/D/g/s/w/init > npm init -y
Wrote to /home/mattc/Documents/gith/std/wasm/init/package.json:

{
  "name": "init",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "Matt Chang",
  "license": "ISC",
  "description": ""
}



~/D/g/s/w/init > npm i webpack webpack-cli

added 118 packages, and audited 119 packages in 5s

16 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
~/D/g/s/w/init > npm i -D webpack-dev-server

added 189 packages, and audited 308 packages in 7s

54 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

Create `.gitignore` as below:

```gitignore
# .gitignore 

node_modules
```

Create `webpack.config.js`:

```js
// webpack.config.js 

const path = require('path');

module.exports = {
  entry: './index.js', // entry javascript file, webpack will compile this file

  // output the compiled file to `public/index.js`
  output: {
    path: path.resolve(__dirname, 'public'),
    filename: 'index.js'
  },

  mode: 'development',  // webpack will compile the code to best suit development environment
}
```


Then, we can add the compiled file to `index.html`:

```html
<!-- public/index.html -->

<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <h1>Hello World</h1>
  <script src="./index.js"></script>
          <!-- ^^^^^^^^^^ add compiled file here by script tag -->
</body>

</html>
```

Add script to start dev server in `package.json`:
```json
{
  "name": "init",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "dev": "webpack-dev-server"  // add this
  },
  "keywords": [],
  "author": "Matt Chang",
  "license": "ISC",
  "description": "",
  "dependencies": {
    "webpack": "^5.96.1",
    "webpack-cli": "^5.1.4"
  },
  "devDependencies": {
    "webpack-dev-server": "^5.1.0"
  }
}
```

Now, we already set up the project, we can run the project by command:

```bash
~/D/g/s/w/init > npm run dev

> init@1.0.0 dev
> webpack-dev-server

<i> [webpack-dev-server] Project is running at:
<i> [webpack-dev-server] Loopback: http://localhost:8080/, http://[::1]:8080/
                                #  ^^^^^^^^^^^^^^^^^^^^^^ check this url on the browser, make sure that fn sum still works
<i> [webpack-dev-server] On Your Network (IPv4): http://192.168.39.53:8080/
<i> [webpack-dev-server] Content not from webpack is served from '/home/mattc/Documents/gith/std/w
asm/init/public' directory
asset index.js 274 KiB [emitted] (name: main)
runtime modules 27.4 KiB 12 modules
modules by path ./node_modules/ 185 KiB
  modules by path ./node_modules/webpack-dev-server/client/ 81.9 KiB 17 modules
  modules by path ./node_modules/webpack/hot/*.js 5.17 KiB
    ./node_modules/webpack/hot/dev-server.js 1.94 KiB [built] [code generated]
    ./node_modules/webpack/hot/log.js 1.73 KiB [built] [code generated]
    + 2 modules
  modules by path ./node_modules/html-entities/lib/*.js 78.9 KiB
    ./node_modules/html-entities/lib/index.js 4.84 KiB [built] [code generated]
    ./node_modules/html-entities/lib/named-references.js 73.1 KiB [built] [code generated]
    ./node_modules/html-entities/lib/numeric-unicode-map.js 389 bytes [built] [code generated]
    ./node_modules/html-entities/lib/surrogate-pairs.js 583 bytes [built] [code generated]
  ./node_modules/ansi-html-community/index.js 4.16 KiB [built] [code generated]
  ./node_modules/events/events.js 14.5 KiB [built] [code generated]
./index.js 651 bytes [built] [code generated]
webpack 5.96.1 compiled successfully in 253 ms
```
