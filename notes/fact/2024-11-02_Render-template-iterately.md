---
date: 2024-11-02
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Render template iterately

```svelte
<script lang="ts">
	let userIDs = $state(['1730524885871', '1730524885872']);
  //            ^^^^^^^ rendered array is reactive, so whenever the array changes, the component will re-render
</script>

<!-- Render userIDs iterately by using `each` block -->
{#each userIDs as userID}
	<p>{userID}</p>
{/each}

<button onclick={() => userIDs.push(new Date().getTime().toString())}>Add User</button>

```

If the template in the `each` block is complex, we can extract it to a [[2024-11-02_Using-snippet-in-the-svelte-file|snippet and use it]] in the `each` block:

```svelte
<script lang="ts">
	let userIDs = $state(['1730524885871', '1730524885872']);
</script>

<!-- create a snippet for reusable template for the `each` block -->
{#snippet userInfo(userid: string)}
	<p>{userid}</p>
{/snippet}

{#each userIDs as userID}
	   {@render userInfo(userID)}
<!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^ use the snippet in the `each` block -->
{/each}

<button onclick={() => userIDs.push(new Date().getTime().toString())}>Add User</button>

```
