---
date: 2025-02-23
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Better structure for components

```sh
 tree -I node_modules/
.
├── package.json
├── package-lock.json
├── README.md
├── src
│   ├── app.css
│   ├── app.d.ts
│   ├── app.html
│   ├── assets
│   │   ├── app-logo.svg
│   │   └── hero.png
│   ├── lib
│   │   └── components
│   │       ├── HeroSection.svelte # this is not reusable but course video put it here
│   │       ├── Button.svelte
│   │       └── index.ts
│   └── routes
│       ├── +layout.svelte
│       └── +page.svelte
├── static
│   └── favicon.png
├── svelte.config.js
├── tsconfig.json
└── vite.config.ts
```

According to the principle of colocation, we should move `HeroSection.svelte` to the `src/routes/components` directory, and the `src/lib` directory should be for reusable things.

```sh
 tree -I node_modules/
.
├── package.json
├── package-lock.json
├── README.md
├── src
│   ├── app.css
│   ├── app.d.ts
│   ├── app.html
│   ├── assets
│   │   ├── app-logo.svg
│   │   └── hero.png
│   ├── lib # only for reusable things
│   │   └── components
│   │       ├── Button.svelte
│   │       └── index.ts
│   └── routes
│       ├── components # create this directory for only this route
│       │   ├── HeroSection.svelte # move HeroSection.svelte here
│       │   └── index.ts
│       ├── +layout.svelte
│       └── +page.svelte
├── static
│   └── favicon.png
├── svelte.config.js
├── tsconfig.json
└── vite.config.ts
```



