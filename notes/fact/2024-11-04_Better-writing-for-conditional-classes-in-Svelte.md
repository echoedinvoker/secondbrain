---
date: 2024-11-04
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Better writing for conditional classes in Svelte

In react, we only can use string template to write conditional classes.

```svelte
<script>
  let isON = $state(false)
</script>

<div class={`mb-l ${isOn ? 'on' : ''}`}>
<!--                       ^^^^  this is conditional class, only work when state `isOn` is true -->
  Some text...
</div>

<button onclick={() => isOn = !isOn}>Toggle</button>

<style>
  .on {
    background-color: red;
  }
</style>

```

In Svelte, we can use `class:` directive to write conditional classes more clearly.

```svelte
<script>
  let isON = $state(false)
</script>

<div
  class="mb-l"
  class:on={isOn}>
<!--   ^^^^^^^^^^ this is conditional class, only work when state `isOn` is true -->
<!--              It seperates the conditional class from the normal class, which makes the code more readable -->

  Some text...
</div>

<button onclick={() => isOn = !isOn}>Toggle</button>

<style>
  .on {
    background-color: red;
  }
</style>

```

[[2024-11-04_Chapter-preview:-turn-static-template-to-dynamic|Here]] is a real world example of `class:` directive in Svelte.
```
