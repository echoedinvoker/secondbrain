---
date: 2024-11-02
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Using snippet in the svelte file

```svelte
<script lang="ts">
	let userName = $state('Matt Chang');  // snippet can access all things in the script
</script>

<!-- snippet is a function to create reusable template -->
{#snippet userInfo(description: string)}  
<!--               ^^^^^^^^^^^^^^^^^^  parameter of the snippet, it only allow one parameter -->
<!--                                   if there is requirement of multiple parameter, use object -->
  
	<p>Logged in as {userName}</p>
	<p>{description}</p>
{/snippet}

<!-- use the snippet by calling it with `@render` -->
{@render userInfo('User details here')}


<!-- styles of this file also apply to the snippet -->
<style>
	p {
		color: blue;
	}
</style>

```

