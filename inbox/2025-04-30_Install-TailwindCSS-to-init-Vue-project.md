---
date: 2025-04-30
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Install TailwindCSS to init Vue project

## Before installing TailwindCSS, Clean up the Vue project (Optional)
```sh
 tree -I node_modules/
.
├── env.d.ts
├── eslint.config.ts
├── index.html
├── package.json
├── package-lock.json
├── public
├── README.md
├── src
│   ├── App.vue
│   ├── assets
│   │   ├── base.css # remove
│   │   ├── logo.svg # remove
│   │   └── main.css # clear all content but keep the file
│   ├── components # remove all files inside but keep the folder
│   │   ├── HelloWorld.vue
│   │   ├── icons
│   │   │   ├── IconCommunity.vue
│   │   │   ├── IconDocumentation.vue
│   │   │   ├── IconEcosystem.vue
│   │   │   ├── IconSupport.vue
│   │   │   └── IconTooling.vue
│   │   ├── TheWelcome.vue
│   │   └── WelcomeItem.vue
│   └── main.ts
├── tsconfig.app.json
├── tsconfig.json
├── tsconfig.node.json
└── vite.config.ts
```

And modify `src/App.vue` to only contain the following code:

```vue
<template>
  <main>
    Hello
  </main>
</template>
```

## Install TailwindCSS v4

TailwindCSS v4 no longer requires a configuration file, so there is no need to use `npx tailwindcss init -p` to generate `tailwind.config.js` and `postcss.config.js`. Just installing TailwindCSS is enough.

Follow the official documentation to install TailwindCSS v4: [link](https://tailwindcss.com/docs/installation/using-vite)

```sh
 npm install tailwindcss @tailwindcss/vite
```
```typescript
// vite.config.ts

// ...

import tailwindcss from '@tailwindcss/vite' // Import the TailwindCSS Vite plugin

export default defineConfig({
  plugins: [
    // ...
    tailwindcss()  // Add this line
  ],

  // ...
})
```

```css
/*src/assets/main.css*/

@import "tailwindcss";
```
