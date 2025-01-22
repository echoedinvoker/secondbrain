---
date: 2024-12-08
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Get action return from backend to frontend

In [[2024-12-08_Pass-form-data-to-backend-by-action|this topic]], we know how to pass form data from the frontend to the backend. Now, we will get the return from the backend to the frontend in action flow.

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
│       ├── AuthForm.svelte  # component which really need the return object...
│       ├── Button.svelte
│       ├── Header.svelte
│       ├── HeroSection.svelte
│       └── index.ts
└── routes
    ├── +layout.svelte
    ├── login
    │   └── +page.svelte
    ├── +page.svelte
    └── register
        ├── +page.server.ts  # action return object from here (backend)
        └── +page.svelte     # get the return object here (frontend) as $page.form

```


```ts
// src/routes/register/+page.server.ts

import type { Actions } from './$types';

export const actions = {
  default: async ({ request }) => {

    const formData = await request.formData();

    const name = formData.get('name') as string;
    const email = formData.get('email') as string;
    const password = formData.get('password') as string;
    const passwordConfirm = formData.get('passwordConfirm') as string;

    // Below is a simple verification, and return an object,
    // the content of this object will be parsed by SvelteKit to get type ActionData.
    if (name.length < 3) {
      return {
        success: false,
        message: 'Name must be at least 3 characters long',
      };
    }
    return {  // no difference between success and error, just return the object
              // with the same structure, we can add a field to present the result status
              // like `success: true` or `success: false` in this case
      success: true,
      message: 'Account created successfully',
    }
  }
} satisfies Actions;

```

Returned object by the form action in the backend will be passed to the frontend as `$page.form` object, which means we can get it by `$props()` in the frontend page component.

```svelte
<!-- src/routes/register/+page.svelte -->

<script lang="ts">
  import { AuthForm } from "$components";

  let { form } = $props(); // get the return object from the backend action

  $inspect(form);
  // when page is just loaded, console output: init null
  // when we submit the form with name less than 3 characters, console output:
  // init {success: false, message: 'Name must be at least 3 characters long'}
  // ^^^^ still init, which means the page is reloaded, not updated

</script>

<AuthForm isRegister {form} />
                <!-- ^^^^^^ pass it to the component which really need it -->

```

We may only need to use the content of `form` in places other than the page component, be careful with the setting of `ActionData` type, and make sure to import from the correct directory.

You can check [official doucment](https://svelte.dev/docs/kit/form-actions#Anatomy-of-an-action) for more details.

```svelte
<!-- src/lib/components/AuthForm.svelte -->

<script lang="ts">
  import { Button } from "$components";
  import type { ActionData } from "./../../routes/register/$types"; // import the parsed type
  //                               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ directory must be correct

  interface AuthFormProps {
    isRegister?: boolean; 
    form?: ActionData;  // accept the return object, and type it with the parsed type ActionData
  }

  let { isRegister, form }: AuthFormProps = $props();
  //                ^^^^
</script>

<div class="auth-container default-margin">
  <h1 class="mb-l">{isRegister ? "Register" : "Login"}</h1>
  <div class="form-and-social-login">
    <form class="auth-form" method="POST">
      {#if isRegister}

...(omitted)...

```

Here we just demonstrate how to get the return object from the backend to the frontend in the action flow. So above code doesn't use `form` to do anything.
```svelte
