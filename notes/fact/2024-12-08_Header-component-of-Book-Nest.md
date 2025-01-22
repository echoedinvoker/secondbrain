---
date: 2024-12-08
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Header component of Book Nest

Create a new file `Header.svelte` in `src/lib/components` for the header component.

```svelte
<script lang="ts">
  import bookNextLogo from '$assets/app-logo.svg';
  import { Button } from '$components';
</script>

<header>
  <a href="/">
    <img class="logo " src={bookNextLogo} alt="Go to home">
                                     <!-- ^^^^^^^^^^^^^^^^ img is wrapped by anchor tag, so its alt attr should be descriptive -->
  </a>
  <nav>
    <ul>
      <li>
        <Button isMenu href="/register">Create account</Button>
           <!-- ^^^^^^ `isMenu={true}` can be simplified to `isMenu` only -->
      </li>
      <li>
        <Button isMenu isSecondary href="/login">Login</Button>
      </li>
    </ul>
  </nav>
</header>

<style>
  header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 12px 4vw;
  }
  ul {
    display: flex;
    gap: 24px;
  }
  .logo {
    height: 72px;
  }
</style>
```

```ts
// src/lib/components/index.ts
// ...
export { default as Header } from './Header.svelte';

```

Because header should be rendered on every page, we can include it in the root layout component.

```svelte
<!-- src/routes/+layout.svelte  -->

<script lang="ts">
  import './../app.css'
  import { Header } from '$components'

  let { children } = $props()
</script>

<Header />
{@render children()}

```
