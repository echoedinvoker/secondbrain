---
date: 2024-12-13
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Render by context

In [[2024-12-13_Use-context-to-publish-class-UserState|this topic]], we have created a context to store session, user and supabase client and completed the setup.

Now, we want to let the header actions to be rendered based on the user existence. If the user is logged in, we will show the logout button, otherwise, we will show the login button.

```svelte
<!-- src/lib/components/Header.svelte  -->

<script lang="ts">
  import bookNextLogo from '$assets/app-logo.svg';
  import { Button } from '$components';
  import { getUserState } from './state/user-state.svelte'; // import the context function

  let userState = getUserState()  // use the context function to get the instance of the class
  let { user } = $derived(userState) // extract user from the instance
              // ^^^^^^^^ remember to use $derived to get the reactive value, otherwise
              //          value of user won't be updated when the instance is updated
</script>

<header>
  <a href="/">
    <img class="logo " src={bookNextLogo} alt="Go to home">
  </a>
  <nav>

    <!-- use `user` as condition to render a logout button if user is logged in, -->
    <!-- otherwise, render login and register buttons -->
    {#if user}
      <ul>
        <li>
          <Button isMenu={true} href="/logout">Logout</Button>
        </li>
      </ul>
    {:else}
      <ul>
        <li>
          <Button isMenu href="/register">Create account</Button>
        </li>
        <li>
          <Button isMenu isSecondary href="/login">Login</Button>
        </li>
      </ul>
    {/if}
  </nav>
</header>

```

Then, we can click the button to login and logout, and the header will be rendered based on the user existence dynamically.
