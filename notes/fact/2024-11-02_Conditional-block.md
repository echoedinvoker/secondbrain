---
date: 2024-11-02
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Conditional block

```svelte
<script lang="ts">
	let input = $state('');
	let isEditing = $state(false);
//                ^^^^^^ condition of {#if} block must be reactive state, so it can be tracked by Svelte
</script>

{#if isEditing}
<!-- ^^^^^^^^^ only render this block if isEditing is true -->
  
	<input type="text" bind:value={input} />

{:else}
<!-- {:else} and {:else if} are optional, sometimes you can just use {#if}{/if} -->
  
	<p>{input}</p>

{/if}

<button onclick={() => (isEditing = !isEditing)}>
	{isEditing ? 'Save' : 'Edit'}
</button>

```

