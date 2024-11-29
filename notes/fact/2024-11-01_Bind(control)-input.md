---
date: 2024-11-01
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Bind(control) input

```svelte
<script lang="ts">
	let name = $state('');
</script>

<input type="text" bind:value={name} />
<!--               ^^^^^  Using `bind:` to control the input property `value` with state `name` -->

<p>Hello {name}</p>

```

