---
date: 2024-11-02
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Debugging with inspect rune

```svelte
<script lang="ts">
	let name = $state('');

	$effect(() => {
		console.log('name', name);  // If you just want to log a state
	});                           // you can use $inspect instead of $effect
</script>

<input type="text" bind:value={name} />

```

```svelte
<script lang="ts">
	let name = $state('please type your name');

	$inspect(name);  // $inspect just simply logs the state
</script>

<input type="text" bind:value={name} />

```

The browser console:

```bash
init name please type your name
update m
update ma
update mar
update mark

```

