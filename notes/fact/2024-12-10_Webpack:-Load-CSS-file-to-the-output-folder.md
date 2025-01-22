---
date: 2024-12-10
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# Webpack: Load CSS file to the output folder

Continuous with [[2024-12-10_Change-entry-and-output-filename-of-Webpack|this topic]], if we want to create a file for CSS styles, we will meet some obstacles.

```css
/* src/app.css */

h1 {
  color: red;
}
```

But it will not be loaded to the output folder after running `npm run build`, so even we add link to the `index.html` file, it will not be applied.

In fact, any files under `src` should be loaded to the output folder by Webpack in order to be used. Webpack can't do anything for CSS files by default, so we need to install a loader for it.

```bash
~/D/g/s/t/webpack-tailwind-starter > npm i -D style-loader css-loader # install loaders for CSS files
```
```js
// webpack.config.js

const path = require("path")

module.exports = {
  mode: "development",
  entry: "./src/app.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "main.js"
  },

  // add this part to tell Webpack how to load CSS files
  module: {
    rules: {
      test: /\.css$/i,  // any file ends with .css
      include: path.resolve(__dirname, "src"),  // only look for CSS files under src directory
      // use which loaders to load the CSS file because Webpack can't do it by default
      use: [
        "style-loader",
        "css-loader"
      ]
    }
  }
}
```

Because Webpack starts bundling from the ONLY ONE entry point, files that are not imported in the entry file will not be processed by Webpack. Therefore, we need to import `app.css` in `app.js` in order for Webpack to process it.

```js
// src/app.js

import './app.css'; // import CSS file into entry file so that Webpack can process it

console.log('Hello, World!');

```

```bash
~/D/g/s/t/webpack-tailwind-starter > npm run build
~/D/g/s/t/webpack-tailwind-starter > tree -I 'node_modules'
.
├── dist
│   ├── index.html
│   └── main.js  # styles are included in the main.js file
├── package.json
├── package-lock.json
├── src
│   ├── app.css
│   └── app.js
└── webpack.config.js
```
```js
// dist/main.js

/*
 * ATTENTION: The "eval" devtool has been used (maybe by default in mode: "development").
 * This devtool is neither made for production nor for readable output files.
 * It uses "eval()" calls to create a separate source file in the browser devtools.
 * If you are trying to read the output file, select a different devtool (https://webpack.js.org/configuration/devtool/)
 * or disable the default devtool with "devtool: false".
 * If you are looking for production-ready output files, see mode: "production" (https://webpack.js.org/configuration/mode/).
 */
/******/ (() => { // webpackBootstrap
/******/        "use strict";
/******/        var __webpack_modules__ = ({

/***/ "./node_modules/css-loader/dist/cjs.js!./src/app.css":
/*!***********************************************************!*\
  !*** ./node_modules/css-loader/dist/cjs.js!./src/app.css ***!        <------------------- app.css codes are compiled here
  \***********************************************************/
/***/ ((module, __webpack_exports__, __webpack_require__) => {

eval("__webpack_require__.r(__webpack_exports__);\n/* harmony export */ __webpack_require__.d(__we
bpack_exports__, {\n/* harmony export */   \"default\": () => (__WEBPACK_DEFAULT_EXPORT__)\n/* har
mony export */ });\n/* harmony import */ var _node_modules_css_loader_dist_runtime_noSourceMaps_js
__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__(/*! ../node_modules/css-loader/dist/runtime/no
SourceMaps.js */ \"./node_modules/css-loader/dist/runtime/noSourceMaps.js\");\n/* harmony import *
/ var _node_modules_css_loader_dist_runtime_noSourceMaps_js__WEBPACK_IMPORTED_MODULE_0___default =
 /*#__PURE__*/__webpack_require__.n(_node_modules_css_loader_dist_runtime_noSourceMaps_js__WEBPACK
_IMPORTED_MODULE_0__);\n/* harmony import */ var _node_modules_css_loader_dist_runtime_api_js__WEB
PACK_IMPORTED_MODULE_1__ = __webpack_require__(/*! ../node_modules/css-loader/dist/runtime/api.js 
*/ \"./node_modules/css-loader/dist/runtime/api.js\");\n/* harmony import */ var _node_modules_css
_loader_dist_runtime_api_js__WEBPACK_IMPORTED_MODULE_1___default = /*#__PURE__*/__webpack_require_
_.n(_node_modules_css_loader_dist_runtime_api_js__WEBPACK_IMPORTED_MODULE_1__);\n// Imports\n\n\nv
ar ___CSS_LOADER_EXPORT___ = _node_modules_css_loader_dist_runtime_api_js__WEBPACK_IMPORTED_MODULE
_1___default()((_node_modules_css_loader_dist_runtime_noSourceMaps_js__WEBPACK_IMPORTED_MODULE_0__
_default()));\n// Module\n___CSS_LOADER_EXPORT___.push([module.id, `h1 {\n  color: red;\n}\n`, \"\    /* our style is here */
"]);\n// Exports\n/* harmony default export */ const __WEBPACK_DEFAULT_EXPORT__ = (___CSS_LOADER_E
XPORT___);\n\n\n//# sourceURL=webpack://webpack-tailwind-starter/./src/app.css?./node_modules/css-
loader/dist/cjs.js");

/***/ }),

/* ... */

```

```bash
~/D/g/s/t/webpack-tailwind-starter > cd dist/
~/D/g/s/t/w/dist > live-server # check the output in browser, the styles are applied
```

