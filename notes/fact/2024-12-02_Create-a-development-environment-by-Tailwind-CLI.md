---
date: 2024-12-02
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# Create a development environment by Tailwind CLI


## Ways to start a Tailwind project

There are many ways to start a Tailwind project.

**1. Tailwind CLI**

When we want to create a static page without using any build tool, we can use Tailwind CLI to create a simple Tailwind project.

![twcli-1.png](../assets/imgs/twcli-1.png)

**2. Load Tailwind CSS as PostCSS plugin**

When we are using build tools such as Webpack, Vite, etc., it is more suitable to use this method.

![twcli-2.png](../assets/imgs/twcli-2.png)


**3. Use Tailwind CSS CDN**

Usually for teaching purposes, and not suitable for use in a production environment.



## Create a simple Tailwind project using Tailwind CLI

Below we are going to create a very simple project, with only one .html file. In this case, we can use Tailwind CLI to create the project.

```bash
~/D/g/s/tailwindcss > mkdir simple-tailwind-starter
~/D/g/s/tailwindcss > cd simple-tailwind-starter/
~/D/g/s/t/simple-tailwind-starter > npm init -y  # create a package.json file so we can install tailwindcss as devDependencies
~/D/g/s/t/simple-tailwind-starter > npm install -D tailwindcss  # install tailwindcss package as devDependencies
~/D/g/s/t/simple-tailwind-starter > npx tailwindcss init  # create a tailwind.config.js file, which is the configuration file for tailwindcss
```
```js
// tailwind.config.js 
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./*.html"],
  //        ^^^^^^^^^^  means any .html files in the root directory will be processed by tailwindcss
  theme: {
    extend: {},
  },
  plugins: [],
}
```

Create an `input.css` file:

```css
/* input.css  */
    @tailwind base;  /* need to be compiled to the final css file with `tailwindcss` command */
    @tailwind components;
    @tailwind utilities;
/*  ^^^^^^^^^ if there is error hinting in the editor, you can just ignore it or install PostCSS extension in your editor */
```

Add scripts to compile the `input.css` file to `css/styles.css` in the `package.json` file:

```json
{
  "name": "simple-tailwind-starter",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "build": "tailwindcss -i ./input.css -o ./css/styles.css",   // compile the input.css file to css/styles.css
    "watch": "tailwindcss -i ./input.css -o ./css/styles.css --watch"   // same, but watch the changes
      
  },
  "keywords": [],
  "author": "Matt Chang",
  "license": "ISC",
  "description": "",
  "devDependencies": {
    "tailwindcss": "^3.4.15"
  }
}
```

```bash
~/D/g/s/t/simple-tailwind-starter > npm run build  # compile the input.css file to css/styles.css
```

Create an `index.html` file:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <link rel="stylesheet" href="css/style.css">  <!-- load compiled .css file -->

  <title>Simple Tailwind Starter</title>
</head>
<body>
  <h1 class="text-3xl">Tailwind CSS Starter</h1>  <!-- use tailwindcss classes for testing -->
</body>
</html>
```

We can use node global package `live-server` to serve the project with hot reload:

```bash
~/D/g/s/t/simple-tailwind-starter > sudo npm install -g live-server  # install live-server globally
~/D/g/s/t/simple-tailwind-starter > live-server  # by default, it will serve `index.html` file in the current directory

Serving "/home/mattc/Documents/gith/std/tailwindcss/simple-tailwind-starter" at http://127.0.0.1:8080
```

Then, we should open another terminal to run the script to watch the changes of the `input.css` file:

```bash
~/D/g/s/t/simple-tailwind-starter > npm run watch  # watch the changes of the input.css file
                                                   # tailwindcss will compile it whenever it is changed

> simple-tailwind-starter@1.0.0 watch
> tailwindcss -i ./input.css -o ./css/styles.css --watch

```
