---
date: 2024-11-03
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Add Usefull utility classes in the global CSS
 
```css src/app.css 

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

.purple {
  color: #3233a6;
}

.white {
  color: white;
}

.light-grey {
  color: #aaaaaa;
}

.landing-page-section {
  padding: 80px 0;
  margin-left: 12vw;
  margin-right: 20vw;
  max-width: 1150px;
}
```

```svelte cat src/routes/+layout.svelte 

<script lang="ts">
  import './../app.css'; // import Global CSS

  let { children } = $props();
</script>

{@render children()}

```

You can also directly use tailwindcss, which will provide almost all imaginable utility classes.

