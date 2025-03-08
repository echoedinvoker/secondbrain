---
date: 2024-12-08
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create structure of register and login pages of Book Nest

We want to create new pages for the register and login of the Book Nest application. They should be very similar, so we can create a reusable component for them.

```bash
~/D/g/s/s/book-nest > tree src/
src/
├── app.css
├── app.d.ts
├── app.html
├── assets
│   ├── app-logo.svg
│   └── hero.png
├── lib
│   └── components
│       ├── AuthForm.svelte # reusable component, it includes header, form and social login
│       ├── Button.svelte
│       ├── Header.svelte
│       ├── HeroSection.svelte
│       └── index.ts # import AuthForm to index.ts and export it
└── routes
    ├── +layout.svelte
    ├── login # login page
    │   └── +page.svelte
    ├── +page.svelte
    └── register # register page
        └── +page.svelte

```

```svelte
<!-- src/lib/components/AuthForm.svelte  -->

<script lang="ts">
    import { Button } from "$components";

  interface AuthFormProps {
    isRegister?: boolean; // there are some differences between register and login, so we can accept a prop to
  }                       // tell the component which one is being used

  let { isRegister }: AuthFormProps = $props();
</script>

<div class="auth-container">
  <h1>{isRegister ? "Register" : "Login"}</h1>
  <div class="form-and-social-login">
    <form>
      <input type="text" name="name" placeholder="Name" />
      <input type="text" name="email" placeholder="Email" />
      <input type="password" name="password" placeholder="Password" />
      <input type="password" name="passwordConfirm" placeholder="Confirm Password" />
      <Button type="submit">{isRegister ? "Register" : "Login"}</Button>
         <!-- ^^^^^^^^^^^^^ when Button is for submit form, there is no href or onclick prop needed, only type -->
         <!--               so we need to adjust the type of ButtonProps interface in Button.svelte -->
    </form>
    <div class="social-login">
    </div>
  </div>
</div>

```

```svelte
<!-- src/lib/components/Button.svelte  -->

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
    onclick?: () => void;
      <!-- ^ make onclick optional because we don't need it for submit button -->

    <!-- add option type for submit button -->
    type?: "submit" | "button";
  }
  type ComponentProps = AnchorProps | ButtonProps;

  ...
</script>
...
```

Next, in login page, form should only have email and password inputs, so we need to remove other inputs by checking the value of isRegister prop.

```svelte
<script lang="ts">
    import { Button } from "$components";

  interface AuthFormProps {
    isRegister?: boolean; 
  }

  let { isRegister }: AuthFormProps = $props();
</script>

<div class="auth-container">
  <h1>{isRegister ? "Register" : "Login"}</h1>
  <div class="form-and-social-login">
    <form>
      <!-- only show name input if isRegister is true -->
      {#if isRegister}
        <input type="text" name="name" placeholder="Name" />
      {/if}

      <input type="text" name="email" placeholder="Email" />
      <input type="password" name="password" placeholder="Password" />

      <!-- only show passwordConfirm input if isRegister is true -->
      {#if isRegister}
        <input type="password" name="passwordConfirm" placeholder="Confirm Password" />
      {/if}

      <Button type="submit">{isRegister ? "Register" : "Login"}</Button>
    </form>
    <div class="social-login">
    </div>
  </div>
</div>
```

Finally, adding a line of small hint with link at the bottom of the form to let user navigate between register and login pages.

```svelte
<script lang="ts">
    import { Button } from "$components";

  interface AuthFormProps {
    isRegister?: boolean; 
  }

  let { isRegister }: AuthFormProps = $props();
</script>

<div class="auth-container">
  <h1>{isRegister ? "Register" : "Login"}</h1>
  <div class="form-and-social-login">
    <form>
      {#if isRegister}
        <input type="text" name="name" placeholder="Name" />
      {/if}
      <input type="text" name="email" placeholder="Email" />
      <input type="password" name="password" placeholder="Password" />
      {#if isRegister}
        <input type="password" name="passwordConfirm" placeholder="Confirm Password" />
      {/if}
      <Button type="submit">{isRegister ? "Register" : "Login"}</Button>

      <!-- add hint to navigate between register and login pages -->
      {#if isRegister}
        <p class="auth-hint">Already have an account? <a href="/login">Login</a></p>
                                                 <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^ this hint includes a link to navigate to login -->
      {:else}
        <p class="auth-hint">Don't have an account? <a href="/register">Register</a></p>
                                               <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ this hint includes a link to navigate to register -->
      {/if}

    </form>
    <div class="social-login">
    </div>
  </div>
</div>

```

Export the AuthForm component in the index.ts file and import it in the register and login pages.

```ts
// src/lib/components/index.ts 

//...

export { default as AuthForm } from './AuthForm.svelte';

```

Create two new routes(pages) for register and login. And use the AuthForm component in them.

```svelte
<!-- src/routes/register/+page.svelte  -->

<script>
  import { AuthForm } from "$components";
</script>

<AuthForm isRegister />
     <!-- ^^^^^^^^^^ -->

```

```svelte
<!-- src/routes/login/+page.svelte  -->

<script>
  import { AuthForm } from "$components";
</script>

<AuthForm />  <!-- not passing isRegister prop, so it will be false by default -->

```

