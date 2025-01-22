---
date: 2024-12-08
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Generic button of Book Nest

We want to create a generic button component, which can be either `<button>` or `<a>`, depending on whether it has the `href` attribute.

```svelte
<!-- src/lib/components/Button.svelte  -->

<script lang="ts">
  import type { Snippet } from "svelte";

  // type attributes that both <button> and <a> have
  interface BasicProps {
    children: Snippet;  
  }

  // type attributes that <a> has
  interface AnchorProps extends BasicProps {
    href: string;
    onclick?: never;  // when href is present, onclick should not be present
                      // we can use `never` to indicate that the attribute should not be present
  }

  // type attributes that <button> has
  interface ButtonProps extends BasicProps {
    href?: never;  // same as above
    onclick: () => void;
  }

  // union of both types for props
  type ComponentProps = AnchorProps | ButtonProps;

  let { children, href, onclick, ...props }: ComponentProps = $props();
</script>

<!-- if href is present, render <a> element, else render <button> element -->
{#if href} 
  <a {href} class="btn" {...props}>
       <!-- ^^^^^^^^^^^ class `btn` is used for styling both <a> and <button> -->
    {@render children()}
  </a>
{:else}
  <button {onclick} class="btn" {...props}>
    {@render children()}
  </button>
{/if}

```
Next, we need to consider the different classifications of buttons that we will use in our project, and then we need to create dynamic classes based on these classifications so that we can use different button styles according to different needs in different situations.

```svelte
<script lang="ts">
  import type { Snippet } from "svelte";

  interface BasicProps {
    children: Snippet;

    // props for dyanmic classes
    isSecondary?: boolean;
    isDanger?: boolean;
    isMenu?: boolean;
  }
  interface AnchorProps extends BasicProps {
    href: string;
    onclick?: never;
  }
  interface ButtonProps extends BasicProps {
    href?: never;
    onclick: () => void;
  }
  type ComponentProps = AnchorProps | ButtonProps;

  let {
    children,
    href,
    onclick,

    // destructuring props for dynamic classes
    isSecondary,
    isDanger,
    isMenu,

    ...props // remember to spread the rest should always be at the end
  }: ComponentProps = $props();
</script>

{#if href}
  <a
    {href}
    class="btn"

    <!-- dynamic classes -->
    class:btn--secondary={isSecondary}
    class:btn--danger={isDanger}
    class:btn--menu={isMenu}

    {...props}
  >
    {@render children()}
  </a>
{:else}
  <button
    {onclick}
    class="btn"

    <!-- dynamic classes -->
    class:btn--secondary={isSecondary}
    class:btn--danger={isDanger}
    class:btn--menu={isMenu}

    {...props}
  >
    {@render children()}
  </button>
{/if}

```
```svelte
