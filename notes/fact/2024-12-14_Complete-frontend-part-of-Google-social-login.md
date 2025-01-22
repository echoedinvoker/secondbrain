---
date: 2024-12-14
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Complete frontend part of Google social login


## Endpoint for Supabase social login to get the session

Here we need to prepare an endpoint, when Google notifies Supabase of a successful login, Supabase will use this endpoint to allow our SvelteKit application to obtain the session.

```ts
// src/routes/auth/callback/+server.ts 

import { redirect, type RequestHandler } from "@sveltejs/kit";

// the below content comes from the documentation of Supabase
export const GET: RequestHandler = async ({ url, locals: { supabase } }) => {

  // sometimes Supabase will send a code in the query params, maybe for email confirmation or something else
  // and we should exchange this code for a session
  const code = url.searchParams.get('code');
  if (code) await supabase.auth.exchangeCodeForSession(code);

  // simply get the session by supabase client and cookie(?) from the request
  const sessionData = await supabase.auth.getSession();

  // following process based on the session data found or not
  if (!sessionData.data.session) return new Response("Session not found", { status: 400 }); // return 400 status code if no session found
  throw redirect(303, "/private/dashboard"); // redirect to private dashboard if session found
}
```


## Form action for Google social login

```bash
# .env
# add the frontend URL into the environment variable
PUBLIC_FRONTEND_URL=http://localhost:5173  # or the actual frontend URL
```
```ts
// src/routes/login/+page.server.ts

export const actions = {
  signInWithPassword: async ({ request, locals: { supabase } }) => { ... },
//^^^^^^^^^^^^^^^^^^ replace `default` with `signInWithPassword`

//then, we can add the google login action
  googleLogin: async ({ request, locals: { supabase } }) => {
    const { data, error } = await supabase.auth.signInWithOAuth({
      provider: 'google',
      options: {
        redirectTo: `${PUBLIC_FRONTEND_URL}/auth/callback`,  // this is for Supabase to notify the frontend that the login is successful
      }                                                      // we already completed this endpoint in the previous step
    })

    if (error) return fail(400, { message: "Something went wrong in the google login" });

    throw redirect(303, data.url);
                     // ^^^^^^^^ let Supabase tell the frontend which page to redirect to
  }
} satisfies Actions;

```


## Using the form action in the frontend component

```svelte
<!-- src/lib/components/AuthForm.svelte  -->

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
    <form class="auth-form" method="POST" action={isRegister ? '' : '/login/?/signInWithPassword'}>
                                     <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ -->
                                     <!-- because form action `default` of /login is renamed to `signInWithPassword` -->

      {#if form && form.errors?.length}
        {#each form.errors as error}
          <p class="auth-error">{error}</p>
        {/each}
      {/if}

      {#if isRegister}
        <input type="text" name="name" placeholder="Name" value={form?.name || ''} />
      {/if}
      <input type="text" name="email" placeholder="Email" value={form?.email || ''} />
      <input type="password" name="password" placeholder="Password" value={form?.password || ''} />
      {#if isRegister}
        <input type="password" name="passwordConfirm" placeholder="Confirm Password" value={form?.passwordConfirm || ''} />
      {/if}
      <Button type="submit">{isRegister ? "Register" : "Login"}</Button>
      {#if isRegister}
        <p class="auth-hint mt-s">Already have an account? <a href="/login">Login</a></p>
      {:else}
        <p class="auth-hint mt-s">Don't have an account? <a href="/register">Register</a></p>
      {/if}
    </form>
    <div class="social-login">

      <!-- add Google login button here -->
      <form method="POST" action={isRegister ? '/login/?/googleLogin' : '?/googleLogin'}>
                     <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ -->
                     <!-- because AuthForm is used in both login and register page, we need to target the same action from the different pages -->
        <Button type="submit">Log in using Google</Button>
      </form>

    </div>
  </div>
</div>
```
