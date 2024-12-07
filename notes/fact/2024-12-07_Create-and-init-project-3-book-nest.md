---
date: 2024-12-07
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create and init project 3 book nest

**1. Create project by `npx sv create`**

```bash
npx sv create book-nest
#   ^^ this is new cli tool for sveltekit
Need to install the following packages:
sv@0.6.6
Ok to proceed? (y) 

┌  Welcome to the Svelte CLI! (v0.6.6)
│
◇  Which template would you like?
│  SvelteKit minimal
│
◇  Add type checking with Typescript?
│  Yes, using Typescript syntax
│
◆  Project created
│
◇  What would you like to add to your project? (use arrow keys / space bar)
│  none
│
◇  Which package manager do you want to install dependencies with?
│  npm
│
◆  Successfully installed dependencies  # `sv` includes the installation process, so we don't need to run `npm install` manually
│
◇  Project next steps ─────────────────────────────────────────────────────╮
│                                                                          │
│  1: cd book-nest                                                         │
│  2: git init && git add -A && git commit -m "Initial commit" (optional)  │
│  3: npm run dev -- --open                                                │
│                                                                          │
│  To close the dev server, hit Ctrl-C                                     │
│                                                                          │
│  Stuck? Visit us at https://svelte.dev/chat                              │
│                                                                          │
├──────────────────────────────────────────────────────────────────────────╯
│
└  You're all set!

~/D/g/s/sv5 > cd book-nest/
```

**2. Setup global styles (from course template)**

```css
/* src/app.css */

/* Base styles reset */
*,
*::after,
*::before {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  -webkit-tap-highlight-color: transparent;
}

/* Resetting default styles of elements */
/* And use special fonts for different elements */
a {
  color: inherit;
  text-decoration: none;

  &:hover,
  &:focus {
    color: inherit;
    text-decoration: none;
  }
}

ul,
li {
  list-style: none;
  margin: 0;
  padding: 0;
  text-indent: 0;
  list-style-type: 0;
}

button {
  font-family: "Poppins", sans-serif;  /* Use Poppins font for buttons, which is one of sans-serif fonts from Google */
  background-color: transparent;
  border: none;
  cursor: pointer;
  outline: none;
  font-weight: 400;
}

body {
  font-family: "Poppins", sans-serif; /* Use Poppins font for body text (regular text) */
  background-color: #f0eee2; /* set project background color as a light grey */
}

/* TYPOGRAPHY */
h1 {
  font-size: 90px;
  font-family: "EB Garamond", serif; /* Use EB Garamond font for headings, which also is one of serif fonts from Google */
  line-height: 1.1; /* Adjust as needed for spacing */
  font-weight: bold;
}

h2 {
  font-size: 60px;
  font-family: "EB Garamond", serif;
  line-height: 1.2;
  font-weight: bold;
}

h3 {
  font-size: 32px;
  line-height: 1.3;
  font-family: "EB Garamond", serif;
  font-weight: normal;
}

h4 {
  font-family: "Poppins", sans-serif;
  font-size: 24px;
  line-height: 1.3;
  font-weight: normal;
}
p,
li {
  font-size: 20px;
  line-height: 1.4;
}

h1,
h2,
h3,
h4,
h5,
h6,
p {
  margin: 0;
}


/* UTILITY CLASSES */
.small-font {
  font-size: 14px;
}

/* UTILITY CLASSES */
.m-xs {
  margin-top: 8px;
}

.mt-s {
  margin-top: 16px;
}

.mt-m {
  margin-top: 32px;
}

.mt-l {
  margin-top: 64px;
}

.mb-xs {
  margin-bottom: 8px;
}

.mb-s {
  margin-bottom: 16px;
}

.mb-m {
  margin-bottom: 32px;
}

.mb-l {
  margin-bottom: 64px;
}

.bold {
  font-weight: 800;
}

.semi-bold {
  font-weight: 500;
}

.italic {
  font-style: italic;
}

.text-center {
  text-align: center;
}

.default-margin {
  margin: 0px 13vw 0px 13vw;
}

.block {
  display: block;
}

/* FORM ELEMENTS RESET */
input,
textarea {
  font-family: "Poppins", sans-serif;
  font-size: 20px;
  background-color: rgba(0, 0, 0, 0.035);
  border-radius: 8px;
  padding: 4px 12px;
  outline: none;
  border: none;
  min-height: 48px;
}
textarea {
  height: 120px;
}

textarea::placeholder,
input::placeholder {
  font-size: 20px;
  font-weight: 400;
}

a {
  text-decoration: underline;
}

a:hover {
  text-decoration: underline;
}
```

**3. Create root layout component**

```bash
~/D/g/s/s/book-nest > tree src/
src/
├── app.css
├── app.d.ts
├── app.html
├── lib
│   └── index.ts
└── routes
    ├── +layout.svelte  # <- new file
    └── +page.svelte

```

```svelte
<!-- src/routes/+layout.svelte -->

<script lang="ts">
  import './../app.css' // root layout is the best place to import global styles

  let { children } = $props()  
</script>

{@render children()}

```

**4. Install Google fonts**

```html
<!-- src/app.html  -->

<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="icon" href="%sveltekit.assets%/favicon.png" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    %sveltekit.head%

    <!-- below three lines are to install google fonts `EB Garamond` and `Poppins` -->
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link
      href="https://fonts.googleapis.com/css2?family=EB+Garamond:ital,wght@0,400..800;1,400..800&family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;
    1,600;1,700;1,800;1,900&display=swap"
      rel="stylesheet"
    />
  </head>
  <body data-sveltekit-preload-data="hover">
    <div style="display: contents">%sveltekit.body%</div>
  </body>
</html>

```
**5. Make sure the font is correct**

```bash
npm run dev
# go to http://localhost:5173 and check if the font is correct
```

![check-header-font.png](../assets/imgs/check-header-font.png)
![check-p-font.png](../assets/imgs/check-p-font.png)
