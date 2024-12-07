---
date: 2024-11-25
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create a generic Button component with Typescript

```bash
~/D/g/s/s/dev-portfolio > tree src/
src/
├── app.css
├── app.d.ts
├── app.html
├── lib
│   ├── components
│   │   ├── Button.svelte  # add this file, content below
│   │   ├── HeroSection.svelte 
│   │   └── index.ts # export { default as Button } from './Button.svelte';
│   └── index.ts
└── routes
    ├── +layout.svelte
    └── +page.svelte

```

```svelte
<!-- src/lib/components/Button.svelte -->

<script lang="ts">
  import type { Snippet } from "svelte";

  interface ButtonProps {
    children: Snippet;
    //        ^^^^^^^ in Svelte5, children will be treated as a Snippet fn
    onclick: ((e: MouseEvent) => void) | (() => void);
    //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  You can define onclick to Function type, but less type safety
    className?: string;
//  ^^^^^^^^^^^^^^^^^^ Optional, used to extend the default styles
  }

  let { children, className, ...props }: ButtonProps = $props();
  //                         ^^^^^^^^ include onclick and other props (we don't need to use onclick below so don't need to extract it)
</script>

<button class={`btn ${className}`} {...props}>
           <!-- ^^^^^^^^^^^^^^^^ .btn is default style, so className must be added after it, order matters -->
  {@render children()}
</button>

<style>
   /* define default button style here */
  .btn {
    font-family: 'Inter Tight', sana-serif;
    font-weight: 500;
    background-color: black;
    color: white;
    font-size: 24px;
    padding: 14px 40px;
    border-radius: 10px;
  }
</style>
```


Then, we can use the Button component in any other component like this:

```svelte
<!-- src/lib/components/HeroSection.svelte  -->

<script>
  import { goto } from "$app/navigation";
  import { Button } from "$components";

  function onclick() {
    goto("/#contact-form");
  }
</script>

<section class="hero-section default-margin">
  <h3>Hello! I'm Matt</h3>
  <div class="underscore mb-m"></div>
  <h1>I'm a Svelte developer crafting intuitive and performant</h1>
  <h1 class="dark-grey">web experiences.</h1>

  <!-- here we only use the default style, so no need to add className -->
  <Button {onclick}>Let's talk</Button>
</section>

<style>
  .hero-section {
    padding-top: 60px;
  }
</style>

```

[[2024-11-03_Create-a-reusable-button-for-fixed-style|Here]] is a similar topic, but this one is more accurate with Typescript.
