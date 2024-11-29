---
date: 2024-11-01
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Using state rune to create reactive state

```svelte
<script>
	let number = $state(0);
          <!-- ^^^^^^^^^ state rune to create reactive state -->

	function onclick() {
		number++;  // when reactive state is updated, the component will re-render
	}
</script>

<button {onclick}>{number}</button>

```

It is recommended to use rune to define reactive state instead of [[2024-11-01_Create-responsive-state-easily|let]] keyword only.

