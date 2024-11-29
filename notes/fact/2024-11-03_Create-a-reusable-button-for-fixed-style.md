---
date: 2024-11-03
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create a reusable button for fixed style

> !!Note!!, better check [[2024-11-25_Create-a-generic-Button-component-with-Typescript|this topic]] instead of this one.

Usually in the same app, there are many buttons with the same style. For removing DRY codes, you can create a reusable button component so that all buttons can share this style.

```svelte src/lib/components/Button.svelte 

<script lang="ts">
  import type { Snippet } from 'svelte';

  interface ButtonProps extends svelte.JSX.HTMLAttributes<HTMLButtonElement> {
    children: Snippet;   //     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ extends button standard HTML attributes for better user experience
    [key: string]: any;
  }

  let { children, ...props }: ButtonProps = $props();
//    ^^^^^^^^^^^^^^^^^^^^^^ We should make this component as flexible as possible, with a user experience very similar to using the original button, just with added fixed styles.
//                           This is why we used `children` and spread props here
</script>

<button {...props}>
<!--    ^^^^^^^^^^ So that the user can pass any props that they would normally pass to a button, like `type`, `disabled`, `onclick`, etc. -->
  {@render children()}
</button>

<!-- Add fixed styles here, which is the point of this component -->
<style>
  button {
    background-color: black;
    color: white;
    padding: 20px 24px;
    font-weight: normal;
    font-size: 22px;
    text-transform: uppercase;
    transition: all 0.2s;
    border: 1px solid white;
  }

  button:hover {
    background-color: white;
    color: black;
  }
</style>

```

Register it to index.ts for better import:

```ts src/lib/components/index.ts

export { default as Button } from './Button.svelte';

```
