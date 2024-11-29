---
date: 2024-11-01
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# derive state from existing state

```svelte
<script>
	let number = $state(0);

	function onclick() {
		number++;
	}
</script>

<button {onclick}>{number}</button>
<div>{number === 0 ? 'Click the button to increment the number' : `The number is ${number}`}</div>
<!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ write derived state in template directly -->
```

```svelte
<script>
	let number = $state(0);
	let info = $derived(   // we can use $derived to derive state from existing state, it make template more clean
		number === 0 ? 'Click the button to increment the number' : `The number is ${number}`
	);

	function onclick() {
		number++;
	}
</script>

<button {onclick}>{number}</button>
<div>{info}</div>

```

```svelte
<script lang="ts">
	let number = $state(0);
	let info = $derived.by(() => getInfo(number)); // we can also write function to derive state
//                    ^^                         // It is better for more complex logic
  function getInfo(number: number) {             // and be careful to use $derived.by instead of $derived for function
    if (number === 0) {
      return 'Number is 0';
    }
    if (number === 1) {
      return 'Number is 1';
    }
    return 'Number is greater than 1';
  }

	function onclick() {
		number++;
	}
</script>

<button {onclick}>{number}</button>
<div>{info}</div>

```
