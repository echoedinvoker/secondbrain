---
date: 2024-12-10
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# Implement Tailwind CSS to the Webpack project

In [[2024-12-10_Webpack:-Load-CSS-file-to-the-output-folder|this topic]], we have learned how to load CSS files to the output folder. But if we add any Tailwind CSS classes to the CSS files, it won't be compiled to native CSS automatically. So, in this topic, we will learn how to implement Tailwind CSS to the Webpack project.

## Setup Tailwind CSS

First, just setup Tailwind CSS to the project as usual.

```bash
~/D/g/s/t/webpack-tailwind-starter > npm i -D tailwindcss # install Tailwind CSS
~/D/g/s/t/webpack-tailwind-starter > npx tailwindcss init # create Tailwind CSS config file
```

```css
/* src/app.css  */

@tailwind base;
@tailwind components;
@tailwind utilities;

```
We do a bit change to the config file of Tailwind CSS:
```js
// tailwind.config.js

/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ['./dist/*.{html,js}'],
          // ^^^^^^^^^^^^^^^^^^ because only need to convert Tailwind CSS classes in the output folder
  theme: {
    extend: {},
  },
  plugins: [],
}

```

## Using PostCSS to compile Tailwind CSS

Before, we need to add a script to complie Tailwind CSS to the project. But now, we will use PostCSS to compile Tailwind CSS.

PostCSS is a tool for transforming CSS with JavaScript plugins. These plugins can lint your CSS, support variables and mixins, transpile future CSS syntax, inline images, and tailwindcss is one of the plugins used to compile Tailwind CSS.

```bash
~/D/g/s/t/webpack-tailwind-starter > npm i -D postcss postcss-loader postcss-preset-env
                                            # postcss is core of the tool
                                                    # postcss-loader let Webpack to use PostCSS to load CSS files
                                                                   # postcss-preset-env is a PostCSS plugin for transforming CSS for future syntax
                        
                                            # postcss can directly use `tailwindcss` as its plugin
```
Create a PostCSS config file to the project:

```js
// postcss.config.js

const tailwindcss = require('tailwindcss');

module.exports = {
  // define which plugins to use
  plugins: [
    'postcss-preset-env',
    tailwindcss
  ],
}
```

Then, add postcss-loader to the Webpack config file:

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
  module: {
    rules: [
      {
        test: /\.css$/i,
        include: path.resolve(__dirname, "src"),
        use: [
          "style-loader",
          "css-loader",
          "postcss-loader"  // add postcss-loader to the rule
        ]
      }
    ]
  }
}
```
```bash
~/D/g/s/t/webpack-tailwind-starter > npm run build
~/D/g/s/t/webpack-tailwind-starter > cd dist/
~/D/g/s/t/w/dist > live-server # check the output in browser, the Tailwind CSS styles are applied

```
