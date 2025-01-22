---
date: 2024-12-10
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# Webpack dev server

Until [[2024-12-10_Implement-Tailwind-CSS-to-the-Webpack-project|this topic]], we have been using `live-server` to start our app on this topic, so every time we make changes to the code, we have to first use `npm run build` and then use `live-server` in the `dist` directory to start our app. This process is actually quite cumbersome. In fact, there is a tool in the Webpack ecosystem that can simplify this whole process, and that is `webpack-dev-server`.

```bash
~/D/g/s/t/webpack-tailwind-starter > npm i -D webpack-dev-server
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

  // add this block for dev server
  devServer: {
    static: {
      directory: path.resolve(__dirname, 'dist'),
      // it will serve index.html by default, and of course, you can change it
    },
    port: 3000,
    open: true, // auto open browser
    hot: true, // refresh on save
    compress: true,
    historyApiFallback: true,
  },

  module: {
    rules: [
      {
        test: /\.css$/i,
        include: path.resolve(__dirname, "src"),
        use: [
          "style-loader",
          "css-loader",
          "postcss-loader"
        ]
      }
    ]
  }
}
```

Create a new script in `package.json` to start the dev server.
```json
{
  "scripts": {
    "start": "webpack serve"
  }
}
```

```bash
~/D/g/s/t/webpack-tailwind-starter > npm run start

# Server will start at http://localhost:3000
# When you make changes to the code, the browser will automatically refresh
```

Some developers may think that `index.html` should be in the `src` directory, if so, Webpack will also need to load it into the output directory. You can check [[2024-11-28_Move-index.html-out-of-public-folder|this topic]] for more information.
