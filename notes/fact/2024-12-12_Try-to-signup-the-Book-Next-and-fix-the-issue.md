---
date: 2024-12-12
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Try to signup the Book Next and fix the issue

In [[2024-12-12_listen-auth-state-change-in-the-frontend-app|this topic]], we already finished all the necessary steps to implement the auth flow.

And we also have implemented registration form action in the frontend app in [[2024-12-09_Sign-up-Book-Nest-with-Supabase-email-provider|this topic]].

So, we want to try to signup the Book Next app and see if we can get the session:


```svelte
<!-- src/routes/+layout.svelte -->

<script lang="ts">
  import './../app.css'
  import { Header } from '$components'
  import { invalidate } from '$app/navigation'

  let { children, data } = $props()
  const { session, supabase, user } = $derived(data)

  $effect(() => {
    const { data } = supabase.auth.onAuthStateChange((_, newSession) => {
      if (newSession?.expires_at !== session?.expires_at) {
        invalidate('supabase:auth')
      }
    })

    return () => data.subscription.unsubscribe()
  })

  $inspect(session)  // add this line to inspect the session on the console of the browser
</script>

<Header />
{@render children()}

```

Unfortunately, although we successfully signed up, the console still only printed out the initial `init null`, and did not print out the updated session information, indicating that our `onAuthStateChange` was not triggered.

The problem lies in the fact that we created a new supabase client instance independently in the registration form action before, instead of using the instance generated during the Supabase auth flow process. This is why the `onAuthStateChange` event is not triggered. Therefore, we need to change the registration form action to use the supabase instance generated during the Supabase auth flow process.

```ts
// src/routes/register/+page.server.ts 

import type { Actions } from './$types';
// import { createClient } from '@supabase/supabase-js';
// import { PUBLIC_SUPABASE_ANON_KEY, PUBLIC_SUPABASE_URL } from '$env/static/public'
import { fail, redirect } from '@sveltejs/kit';

interface ReturnObject {
  success: boolean;
  errors: string[];
}

export const actions = {
  default: async ({ request, locals: { supabase } }) => {
                          // ^^^^^^^^^^^^^^^^^^^^ supabase client instance is binded to the event.locals.supabase
                                                    
    const formData = await request.formData();

    const name = formData.get('name') as string;
    const email = formData.get('email') as string;
    const password = formData.get('password') as string;
    const passwordConfirm = formData.get('passwordConfirm') as string;

    const returnObject: ReturnObject = {
      success: true,
      errors: [],
    };

    if (name.length < 3) returnObject.errors.push('Name must be at least 3 characters long');
    if (!email.length) returnObject.errors.push('Email is required');
    if (!password.length) returnObject.errors.push('Password is required');
    if (password !== passwordConfirm) returnObject.errors.push('Passwords do not match');
    
    if (returnObject.errors.length) returnObject.success = false;

    // remove the following line, as we already have the supabase instance from the event.locals.supabase
    // const supabase = createClient(PUBLIC_SUPABASE_URL, PUBLIC_SUPABASE_ANON_KEY);

    const { data, error } = await supabase.auth.signUp({ email, password })

    if (error || !data.user) {
      console.log("There was an error signing up", error);
      returnObject.success = true;
      return fail(400, returnObject as any)
    }

    redirect(303, "/private/dashboard");


  }
} satisfies Actions;

```
Try to sign up again, and you will see the updated session information in the console.

```bash
{
    "access_token": "eyJhbGciOiJIUzI1NiIsImtpZCI6IjkyR01vS1lLYzlqME1DUnAiLCJ0eXAiOiJKV1QifQ.eyJpc3MiOiJodHRwczovL29xcGJqcGliZGdzc3l1cXdxYnRzLnN1cGFiYXNlLmNvL2F1dGgvdjEiLCJzdWIiOiIyYmFiY2RhZS04ODUwLTQwMDItODVkZC1lZjY3OTgyYjgxZGMiLCJhdWQiOiJhdXRoZW50aWNhdGVkIiwiZXhwIjoxNzM0MDEyMTQ0LCJpYXQiOjE3MzQwMDg1NDQsImVtYWlsIjoiam95Y2VAbWFpbC5jb20iLCJwaG9uZSI6IiIsImFwcF9tZXRhZGF0YSI6eyJwcm92aWRlciI6ImVtYWlsIiwicHJvdmlkZXJzIjpbImVtYWlsIl19LCJ1c2VyX21ldGFkYXRhIjp7ImVtYWlsIjoiam95Y2VAbWFpbC5jb20iLCJlbWFpbF92ZXJpZmllZCI6ZmFsc2UsInBob25lX3ZlcmlmaWVkIjpmYWxzZSwic3ViIjoiMmJhYmNkYWUtODg1MC00MDAyLTg1ZGQtZWY2Nzk4MmI4MWRjIn0sInJvbGUiOiJhdXRoZW50aWNhdGVkIiwiYWFsIjoiYWFsMSIsImFtciI6W3sibWV0aG9kIjoicGFzc3dvcmQiLCJ0aW1lc3RhbXAiOjE3MzQwMDg1NDR9XSwic2Vzc2lvbl9pZCI6ImQyNTUyN2QyLTI0NmYtNDQ3NC05ZDYyLThjYmU5MTIwNTY3YyIsImlzX2Fub255bW91cyI6ZmFsc2V9.l9c3kQ3p6CnhhNSZ7Lez3qXu2BeSAIobV9m3zyeYJjE",
    "token_type": "bearer",
    "expires_in": 3600,
    "expires_at": 1734012144,
    "refresh_token": "vSbN5qdKrax_-vnF_iWZjw",
    "user": {
        "id": "2babcdae-8850-4002-85dd-ef67982b81dc",
        "aud": "authenticated",
        "role": "authenticated",
        "email": "joyce@mail.com",
        "email_confirmed_at": "2024-12-12T13:02:24.308528048Z",
        "phone": "",
        "last_sign_in_at": "2024-12-12T13:02:24.312146971Z",
        "app_metadata": {
            "provider": "email",
            "providers": [
                "email"
            ]
        },
        "user_metadata": {
            "email": "joyce@mail.com",
            "email_verified": false,
            "phone_verified": false,
            "sub": "2babcdae-8850-4002-85dd-ef67982b81dc"
        },
        "identities": [
            {
                "identity_id": "f1a0e48b-4f07-437f-a257-a34e003a5a77",
                "id": "2babcdae-8850-4002-85dd-ef67982b81dc",
                "user_id": "2babcdae-8850-4002-85dd-ef67982b81dc",
                "identity_data": {
                    "email": "joyce@mail.com",
                    "email_verified": false,
                    "phone_verified": false,
                    "sub": "2babcdae-8850-4002-85dd-ef67982b81dc"
                },
                "provider": "email",
                "last_sign_in_at": "2024-12-12T13:02:24.303772429Z",
                "created_at": "2024-12-12T13:02:24.303825Z",
                "updated_at": "2024-12-12T13:02:24.303825Z",
                "email": "joyce@mail.com"
            }
        ],
        "created_at": "2024-12-12T13:02:24.297415Z",
        "updated_at": "2024-12-12T13:02:24.314608Z",
        "is_anonymous": false
    }
}
```

Then, we can use context to pass the session to the child components.

