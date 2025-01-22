---
date: 2024-12-13
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Prevent to remove input values when submit error

In register form and login form, when user submit the form and there is an error, although the error message is displayed but the input values are removed. This is not a good user experience. We should keep the input values when there is an error.


## Let form actions return the input values also

**form action of register page**
```ts
// src/routes/register/+page.server.ts

import type { Actions } from './$types';
import { fail, redirect } from '@sveltejs/kit';

interface ReturnObject {
  success: boolean;
  errors: string[];

  // add input values to return object
  name: string;
  email: string;
  password: string;
  passwordConfirm: string;
}

export const actions = {
  default: async ({ request, locals: { supabase } }) => {

    const formData = await request.formData();

    const name = formData.get('name') as string;
    const email = formData.get('email') as string;
    const password = formData.get('password') as string;
    const passwordConfirm = formData.get('passwordConfirm') as string;

    const returnObject: ReturnObject = {
      success: true,
      errors: [],

      // add input values to return object
      name,
      email,
      password,
      passwordConfirm,
    };

    if (name.length < 3) returnObject.errors.push('Name must be at least 3 characters long');
    if (!email.length) returnObject.errors.push('Email is required');
    if (!password.length) returnObject.errors.push('Password is required');
    if (password !== passwordConfirm) returnObject.errors.push('Passwords do not match');
    
    if (returnObject.errors.length) returnObject.success = false;


    const { data, error } = await supabase.auth.signUp({ email, password })

    if (error || !data.user) {
      returnObject.success = false;
      return fail(400, returnObject as any)
    }

    redirect(303, "/private/dashboard");
  }
} satisfies Actions;
```

**form action of login page**

```ts
// src/routes/login/+page.server.ts

import type { Actions } from './$types';
import { fail, redirect } from '@sveltejs/kit';

interface ReturnObject {
  success: boolean;
  errors: string[];

  // add input values to return object
  email: string;
  password: string;
}

export const actions = {
  default: async ({ request, locals: { supabase } }) => {

    const formData = await request.formData();

    const email = formData.get('email') as string;
    const password = formData.get('password') as string;

    const returnObject: ReturnObject = {
      success: true,
      errors: [],

      // add input values to return object
      email,
      password,
    };

    if (!email.length) returnObject.errors.push('Email is required');
    if (!password.length) returnObject.errors.push('Password is required');
    
    if (returnObject.errors.length) returnObject.success = false;

    const { data, error } = await supabase.auth.signInWithPassword({ email, password })

    if (error || !data.user) {
      returnObject.success = false;
      return fail(400, returnObject as any)
    }

    redirect(303, "/private/dashboard");


  }
} satisfies Actions;
```

## Update the input values of frontend form

```svelte
<!-- src/lib/components/AuthForm.svelte -->

<script lang="ts">
  import { Button } from "$components";
  import type { ActionData } from "./../../routes/register/$types";


  interface AuthFormProps {
    isRegister?: boolean; 
    form?: ActionData;
  }

  let { isRegister, form }: AuthFormProps = $props();
</script>

<div class="auth-container default-margin">
  <h1 class="mb-l">{isRegister ? "Register" : "Login"}</h1>
  <div class="form-and-social-login">
    <form class="auth-form" method="POST">

      {#if form && form.errors?.length}
        {#each form.errors as error}
          <p class="auth-error">{error}</p>
        {/each}
      {/if}

      {#if isRegister}
        <input type="text" name="name" placeholder="Name" value={form?.name || ''} />
                                                     <!-- ^^^^^^^^^^^^^^^^^^^^^^^^ -->
      {/if}
      <input type="text" name="email" placeholder="Email" value={form?.email || ''} />
                                                     <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^ -->
      <input type="password" name="password" placeholder="Password" value={form?.password || ''} />
                                                               <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ -->
      {#if isRegister}
        <input type="password" name="passwordConfirm" placeholder="Confirm Password" value={form?.passwordConfirm || '' } />
                                                                                <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ -->
      {/if}
      <Button type="submit">{isRegister ? "Register" : "Login"}</Button>
      {#if isRegister}
        <p class="auth-hint mt-s">Already have an account? <a href="/login">Login</a></p>
      {:else}
        <p class="auth-hint mt-s">Don't have an account? <a href="/register">Register</a></p>
      {/if}
    </form>
    <div class="social-login">
    </div>
  </div>
</div>
```
