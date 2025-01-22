---
date: 2024-12-10
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# Minimum Webpack project setup

```bash
~/D/g/s/tailwindcss > mkdir webpack-tailwind-starter
~/D/g/s/tailwindcss > cd webpack-tailwind-starter/
~/D/g/s/t/webpack-tailwind-starter > npm init -y  # create package.json
~/D/g/s/t/webpack-tailwind-starter > npm i -D webpack webpack-cli # webpack is a module bundler and webpack-cli is a command line interface for webpack
```
So we can add a script to use command `webpack` in package.json:
```json
{
  ...
  "scripts": {
    "build": "webpack" # add this line, `webpack` is provided by webpack-cli for us to use webpack to bundle our code
  },
  ...
}
```
But we need to tell webpack how to bundle our code, so we need to create a webpack configuration file `webpack.config.js`:

```js
module.exports = {
  mode: "development",  // We need to at least inform webpack whether the packaged files are for development or production.
                        // It will use better ways to package the files which are more suitable for the environment.
}

```

Now, the build script already works:

```bash
~/D/g/s/t/webpack-tailwind-starter > npm run build
~/D/g/s/t/webpack-tailwind-starter > tree -I 'node_modules'
.
├── dist # by default, webpack will create a dist directory and put the bundled file in it
│   └── main.js  # by default, the bundled file is named main.js
├── package.json
├── package-lock.json
├── src
│   └── index.js # by default, webpack will look for src/index.js as the entry file
└── webpack.config.js

```

Below is the content of the bundled file `main.js`:

```js
/*
 * ATTENTION: The "eval" devtool has been used (maybe by default in mode: "development").
 * This devtool is neither made for production nor for readable output files.
 * It uses "eval()" calls to create a separate source file in the browser devtools.
 * If you are trying to read the output file, select a different devtool (https://webpack.js.org/configuration/devtool/)
 * or disable the default devtool with "devtool: false".
 * If you are looking for production-ready output files, see mode: "production" (https://webpack.js
.org/configuration/mode/).
 */
/******/ (() => { // webpackBootstrap
/******/        var __webpack_modules__ = ({

/***/ "./src/index.js":
/*!**********************!*\
  !*** ./src/index.js ***!
  \**********************/
/***/ (() => {

eval("console.log('Hello, World!');\n\n\n//#ex.js?");
   // ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ our source codes are here

/***/ })

/******/        });
/********************************************
/******/ 
/******/        // startup
/******/        // Load entry module and retu
/******/        // This entry module can't be
/******/        var __webpack_exports__ = {};
/******/        __webpack_modules__["./src/in
/******/ 
/******/ })()

```

Now, we can create an HTML file next to the bundled file `main.js` and load the bundled file in the HTML file:

```html
<!-- dist/index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device"
  <title>Webpack Tailwind Starter</title>
</head>
<body>
  <h1>Hello World!</h1>
  <script src="main.js"></script>
          <!-- ^^^^^^^ load the bundled file here manually -->
</body>
</html>
```
Then, we can still use the `live-server` to serve the HTML file:
```bash
~/D/g/s/t/webpack-tailwind-starter > cd dist
~/D/g/s/t/w/dist > live-server # open the browser and go to the URL provided by live-server
                               # you should see the "Hello World!" in the console which proves the bundled file is working

```


