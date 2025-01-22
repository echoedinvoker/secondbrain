---
date: 2024-12-09
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Complete the auth form errors presentation of Book Nest

In [[2024-12-08_Get-action-return-from-backend-to-frontend|this topic]], we have studied how to get the action return from the backend to the frontend. Now, we will complete backend validation and present the errors to the frontend.

```ts
// src/routes/register/+page.server.ts 

import type { Actions } from './$types';

// add this interface
interface ReturnObject {
  success: boolean;
  errors: string[];
}

export const actions = {
  default: async ({ request }) => {

    const formData = await request.formData();

    const name = formData.get('name') as string;
    const email = formData.get('email') as string;
    const password = formData.get('password') as string;
    const passwordConfirm = formData.get('passwordConfirm') as string;


    // return object may contain multiple errors, so we define it as below before checking each field
    const returnObject: ReturnObject = {
      success: true,
      errors: [],
    };

    // check each field and push the error message to the errors array
    if (name.length < 3) returnObject.errors.push('Name must be at least 3 characters long');
    if (!email.length) returnObject.errors.push('Email is required');
    if (!password.length) returnObject.errors.push('Password is required');
    if (password !== passwordConfirm) returnObject.errors.push('Passwords do not match');
    
    // if there are any errors, set success to false
    if (returnObject.errors.length) returnObject.success = false;

    return returnObject;
  }
} satisfies Actions;

```

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

      <!-- add this block to show errors in the form -->
      {#if form && form.errors?.length}
        {#each form.errors as error}
          <p class="auth-error">{error}</p>
        {/each}
      {/if}

      {#if isRegister}
        <input type="text" name="name" placeholder="Name" />
      {/if}
      <input type="text" name="email" placeholder="Email" />
      <input type="password" name="password" placeholder="Password" />
      {#if isRegister}
        <input type="password" name="passwordConfirm" placeholder="Confirm Password" />
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

<style>
  .auth-container {
    margin-top: 80px;
  }

  .form-and-social-login {
    display: flex;
  }

  .auth-form {
    display: flex;
    flex-direction: column;
    align-items: start;
    border-right: 1px solid grey;
    padding-right: 80px;
    width: 40%;
  }

  .auth-form input {
    width: 100%;
    margin-bottom: 12px;
  }

  .auth-form input:last-of-type {
    margin-bottom: 30px;
  }

  .auth-hint {
    font-size: 16px;
    color: grey;
  }

  .auth-error {
    background-color: rgb(122, 35, 35);
    color: white;
    font-size: 18px;
    border-radius: 12px;
    padding: 12px;
    width: 100%;
    margin-bottom: 8px;
  }

  .auth-error:last-of-type  {
    margin-bottom: 16px;
  }

  .social-login {
    padding-left: 80px;
    width: 40%;
  }
</style>

```
