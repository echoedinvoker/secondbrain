---
date: 2024-10-12
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Initiate the global css file

If you create Next JS project with `npx create-next-app@latest`, you should have a `src/globals.css` file. This file has lots of CSS code just for the demo. So you should remove all the CSS code and keep only the `@tailwind` directive.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

/* Only keep the @tailwind directive in the global CSS file */
/* Remove all other CSS */

```
