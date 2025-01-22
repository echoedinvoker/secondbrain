---
date: 2024-12-08
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Styling the generic button component of Book Nest

After creating [[2024-12-08_Generic-button-of-Book-Nest|generic button of Book Nest]], we want to style it as below:

```svelte
<!-- src/lib/components/Button.svelte -->

<script lang="ts">
  import type { Snippet } from "svelte";

  interface BasicProps {
    children: Snippet;
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
    isSecondary,
    isDanger,
    isMenu,
    ...props
  }: ComponentProps = $props();
</script>

{#if href}
  <a
    {href}

    <!-- static class `btn` is for doing the default styling -->
    class="btn"

    <!-- dynamic classes `btn--secondary`, `btn--danger`, `btn--menu` are for applying the dynamic
 styles -->
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
    class:btn--secondary={isSecondary}
    class:btn--danger={isDanger}
    class:btn--menu={isMenu}
    {...props}
  >
    {@render children()}
  </button>
{/if}

<!-- styling for the button component -->
<style>
  /* reset the default styles of anchor tag */
  a {
    display: block;
    text-decoration: none;
  }
  a:hover {
    text-decoration: none;
  }

  /* if no any dynamic class is applied, then the default class will be applied */
  .btn {
    padding: 12px 24px;
    min-width: 230px;
    text-align: center;
    background-color: black;
    border-radius: 12px;
    color: white;
    border: 1px solid white;
    font-weight: normal;
    font-size: 22px;
  }

  /* default class already defined all the styles */
  /* dynamic classes just need to override some styles of the default class */
  .btn--secondary {
    background-color: white;
    color: black;
    border: 1px solid black;
  }
  .btn--danger {
    background-color: rgb(136, 4, 4); /* only color is changed */
  }
  .btn--menu { /* more tighter button because it's used in the menu */
    min-width: 150px;
    padding: 8px 20px;
  }
</style>

```

```svelte
<!-- src/lib/components/index.ts  -->

export { default as HeroSection } from './HeroSection.svelte';
export { default as Button } from './Button.svelte'; // exporting the generic button component

```

Now, we can use the generic button component in the `HeroSection` component as below:

```svelte
<!-- src/lib/components/HeroSection.svelte  -->

<script lang="ts">
  import heroImage from '$assets/hero.png';
  import { Button } from '$components';  // importing the generic button component
</script>

<section class="hero default-margin">
  <div class="hero-text">
    <h1>Book Nest</h1>
    <h3>Your personal book library.</h3>
    <h4 class="mt-l">Create your very own digital library where you can keep track of every book y ou own, read and love.</h4>
    <h4 class="mb-s">Our app offers a beautifully designed, easy-to-use interface that makes manai ng your book collection a joy.</h4>

    <Button href="/">Sign up</Button> <!-- replace normal button tag with the generic button component -->

  </div>
  <img src={heroImage} alt="" class="hero-image">
</section>

...

```
