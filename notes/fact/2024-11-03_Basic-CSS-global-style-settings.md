---
date: 2024-11-03
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Basic CSS global style settings


```css src/app.css 
*,
*::after,
*::before {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
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

```

```svelte src/routes/+layout.svelte 

<script lang="ts">
  import './../app.css';  // import global styles

  let { children } = $props();
</script>

{@render children()}
```

We usually define [[2024-11-03_Add-Google-font-to-our-project|typography]] and some [[2024-11-03_Add-Usefull-utility-classes-in-the-global-CSS|utility classes]] in Global CSS, but these may not be needed on all pages, so they are not included in the examples above.
