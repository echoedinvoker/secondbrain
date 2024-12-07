---
date: 2024-12-06
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create Header of landing page

After we finished the last section of landing page, we start to create the Header of the landing page, which also exists in every page of the website.


## Structure

```svelte
<!-- src/lib/components/Header.svelte  (new file) -->

<script lang="ts">
  import { goto } from "$app/navigation";
  import { Button } from "$components";

  // create a event handler to navigate to the contact
  const onclick = () => {
    goto("#contact-form");  // use `goto` function from `$app/navigation` to navigate to the contact form
  };
</script>

<nav class="navbar">
  <a href="/" class="logo">MT</a>
  <div class="navbar-links">
    <a href="/#about-me" class="nav-link">Abount Me</a>
    <a href="/#my-work" class="nav-link">Work</a>

    <!-- We want the Button style to create a contact link, so we are using the Button component -->
    <Button className="nav-bar" {onclick}>Contact</Button>
       <!-- ^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^Button is based on `button`, so we need to use event handler to navigate to the contact form. -->
       <!-- ^ -->
       <!-- ^ The default Button style may not be completely suitable for the navbar, so we need to add a class to adjust the style. -->
  </div>
</nav>
```

```ts
// src/lib/components/index.ts

//...
export { default as Header } from './Header.svelte';

```

Because Header component should be rendered in every page, we can add it to the layout component.

```svelte
<!-- src/routes/+layout.svelte  -->

<script lang="ts">
  import '../app.css';
  import { Header } from '$components';

  let { children } = $props();
</script>

<Header /> <!-- Add the Header component to the layout -->
{@render children()}

```

## Styling

```svelte
<!-- src/lib/components/Header.svelte -->

<script lang="ts">
  import { goto } from "$app/navigation";
  import { Button } from "$components";

  const onclick = () => {
    goto("#contact-form");
  };
</script>

<nav class="navbar default-margin">
              <!-- ^^^^^^^^^^^^^^ -->
  <a href="/" class="logo">MT</a>
  <div class="navbar-links">
    <a href="/#about-me" class="nav-link">Abount Me</a>
    <a href="/#my-work" class="nav-link">Work</a>
    <Button className="nav-bar" {onclick}>Contact</Button>
                  <!-- ^^^^^^^ styles of "nav-bar" are not defined here but in Button.svelte -->
  </div>
</nav>

<!-- do styling below: -->
<style>
  .navbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 20px;
  }

  .logo {
    font-size: 1.5rem;
    font-weight: bold;
    color: #333;
  }

  .navbar-links {
    display: flex;
    gap: 20px;
    align-items: center;
  }

  .nav-link {
    font-size: 1rem;
    color: #333;
    text-decoration: none;
  }

  .nav-link:hover {
    text-decoration: underline;
  }
</style>
```

NOTE!! we should define the `nav-bar` class in the Button component instead of the Header component:

```svelte
<!-- src/lib/components/Button.svelte  -->

<script lang="ts">
  import type { Snippet } from "svelte";

  interface ButtonProps {
    children: Snippet;
    onclick?: ((e: MouseEvent) => void) | (() => void);
    className?: string;
  }

  let { children, className, ...props }: ButtonProps = $props();
</script>

<button class={`btn ${className}`} {...props}>
  {@render children()}
</button>

<style>
  .btn {
    font-family: 'Inter Tight', sana-serif;
    font-weight: 500;
    background-color: black;
    color: white;
    font-size: 24px;
    padding: 14px 40px;
    border-radius: 10px;
  }

  /* define the "nav-bar" class here */
  .nav-bar {
    padding: 10px 24px;
    font-size: 20px;
  }
</style>

```

