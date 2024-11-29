---
date: 2024-11-01
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Styles are modulized automatically

```svelte src/routes/+page.svelte 

<script>
import Child from '../components/child.svelte';
</script>

<h1>Page</h1>
<Child />

<!-- styles are modulized automatically, don't leak to the child component, we don't need to add `scoped` -->
<style>
h1 {
    color: red;
}
</style>
```

```svelte src/components/child.svelte 

<h1>Child</h1>
<!-- ^^^^^ not effected by the style in the parent -->

```
```

