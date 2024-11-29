---
date: 2024-11-01
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create responsive state easily

```svelte
<script lang="ts">
	let count = 0;   // simply using `let` to declare a variable, and it will be reactive state

	function onclick() {
		count += 1;
	}
</script>

<button {onclick}>{count}</button>

```
>Be careful, if you define state using Svelte 5's rune in the same file, using `let` to define a variable will just be a regular variable and not a state.
