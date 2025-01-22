---
date: 2024-12-10
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# Change entry and output filename of Webpack

In [[2024-12-10_Minimum-Webpack-project-setup|this topic]], we have seen how to set up a minimum Webpack project. But sometimes you may want to change the entry and output filename of the Webpack. Here is how you can do that:

```bash
~/D/g/s/t/webpack-tailwind-starter > tree -I 'node_modules'
.
├── dist
│     # assume we want to change output filename to main.js
├── package.json
├── package-lock.json
├── src
│   └── app.js
│     # ^^^ default name is index.js, but we have changed it to app.js
└── webpack.config.js

```
We should tell Webpack about the changes, or it will look for the default files. Here is how you can do that:

```js
// webpack.config.js

const path = require("path") // this module is used to convert relative path to absolute path
                             // because certain places require absolute path

module.exports = {
  mode: "development",
  entry: "./src/app.js", // tell Webpack to look for app.js instead of index.js as entry file

  // tell Webpack to output main.js file instead of bundle.js
  output: {
    path: path.resolve(__dirname, "dist"),  // if use `output`, even we just change the filename, we still need to provide the **absolute** path
    filename: "main.js"  // change the output filename to main.js as we want
  }
}
```

```bash
~/D/g/s/t/webpack-tailwind-starter > npm run build
~/D/g/s/t/webpack-tailwind-starter > tree -I 'node_modules'
.
├── dist
│   └── main.js  # main.js file is created
├── package.json
├── package-lock.json
├── src
│   └── app.js
└── webpack.config.js

```
