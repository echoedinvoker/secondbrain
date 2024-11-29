---
date: 2024-11-01
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# props shorthand

```svelte
<script lang="ts">
	function onclick() {
		console.log('clicked');
	}
</script>

<button onclick={onclick}>Click me</button>
  <!-- ^^^^^^^^^^^^^^^^^ props name is same as function name -->

```
We can use props shorthand:

```svelte
<script lang="ts">
  function onclick() {
    console.log('clicked');
  }
</script>

<button {onclick}>Click me</button>
  <!-- ^^^^^^^^^^ props shorthand -->
```


