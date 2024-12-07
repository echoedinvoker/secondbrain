---
date: 2024-11-24
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create new project dev portfolio and init it

```bash
~/D/g/s/sv5 > npx sv create dev-portfolio
┌  Welcome to the Svelte CLI! (v0.6.5)
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
◆  Successfully installed dependencies
│
◇  Project next steps ─────────────────────────────────────────────────────╮
│                                                                          │
│  1: cd dev-portfolio                                                    │
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
```

Create custom css file:

```css
/* src/app.css */

*,
*::after,
*::before {
  margin: 0;
  padding: 0;
  box-sizing: inherit;
  -webkit-tap-highlight-color: transparent;
}

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
  background-color: transparent;
  border: none;
  cursor: pointer;
  outline: none;
  font-weight: 400;
}

body {
  font-family: "Inter Tight", sans-serif;
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

/* Global Typography Styles */

h1 {
  font-size: 80px;
  line-height: 1.1;
  /* Adjust as needed for spacing */
  font-weight: bold;
}

h2 {
  font-size: 60px;
  line-height: 1.2;
  font-weight: bold;
}

h3 {
  font-size: 40px;
  line-height: 1.3;
}

h3 {
  font-size: 22px;
  line-height: 1.3;
  font-weight: normal;
}

p,
li {
  font-size: 16px;
  line-height: 1.4;
}

p {
  margin-bottom: 1rem;
}

/* UTILITY CLASSES */
.dark-grey {
  color: #5e5e5e;
}

.default-margin {
  margin-left: 15vw;
  margin-right: 20vw;
  max-width: 950px;
}

.underscore {
  background-color: #5e5e5e;
  height: 4px;
  width: 40px;
}

.link {
  text-decoration: underline;
}

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
  font-weight: bold;
}

.semi-bold {
  font-weight: 500;
}

```
Add google fonts to `src/app.html`:

```html
<!-- src/app.html -->

<!doctype html>
<html lang="en">

<head>
  <meta charset="utf-8" />
  <link rel="icon" href="%sveltekit.assets%/favicon.png" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  %sveltekit.head%

  <!-- add google fonts below by some link tags -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Inter+Tight:ital,wght@0,100..900;1,100..900
&display=swap"
    rel="stylesheet" />
</head>

<body data-sveltekit-preload-data="hover">
  <div style="display: contents">%sveltekit.body%</div>
</body>

</html>

```

Create root layout file and import global css file there:

```svelte
<!-- src/routes/+layout.svelte  -->

<script lang="ts">
  import '../app.css';

  let { children } = $props();
</script>

{@render children()}

```
