---
date: 2025-04-30
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Use Alias and Absolute Path instead of Relative Path

When using import in the script of Vue, it is recommended to use absolute path so that the path does not need to be adjusted when refactoring.

Before using absolute path, we need to configure the alias in `vite.config.js`:

```javascript
import { fileURLToPath, URL } from 'node:url'

// ...

export default defineConfig({
  plugins: [ ... ],
  resolve: {
    // This is the alias configuration
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url))
      // import.meta.url is the URL of the current module: file:///home/matt/Projects/event-booking/vite.config.ts
      // fileURLToPath converts the URL with the first argument to a file path: /home/matt/Projects/event-booking/src
      // That's what Node.js API needs
    },
  },
})
```

In the above code, we set the alias `@` to point to the `src` directory. So we can use it like this:

```vue
<template>
  <EventCard />
</template>

<script setup lang="ts">
  import EventCard from '@/components/EventCard.vue'
  //                     ^^^^^^^^^^^^^^^^^^^^^^^^^^
  //                     this is equivalent to /home/matt/Projects/event-booking/src/components/EventCard.vue
  //                     which is the absolute path
</script>
```


