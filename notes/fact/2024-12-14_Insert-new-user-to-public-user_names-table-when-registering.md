---
date: 2024-12-14
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Insert new user to public user_names table when registering

In [[2024-12-14_Create-a-public-table-on-Supabase-to-track-auth-users-table|this topic]], although we have created a public.user_names table in Supabase to track the auth.users table, we must implement the logic to insert new users into this table in the app ourselves.

The first best place to insert new user to public.user_names table is when a new user is registered, we can insert codes in the register form action:

```ts
// src/routes/register/+page.server.ts

import type { Actions } from './$types';
import { fail, redirect } from '@sveltejs/kit';

interface ReturnObject {
  success: boolean;
  name: string;
  email: string;
  password: string;
  passwordConfirm: string;
  errors: string[];
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
      name,
      email,
      password,
      passwordConfirm,
      errors: [],
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

    // Insert new user to public.user_names table
    await supabase.from('user_names').insert([
      {
        user_id: data.user.id,
        name,
      }
    ])

    redirect(303, "/private/dashboard");
  }
} satisfies Actions;

```

The CRUD operations to the public tables are very simple, you can check the [official documentation](https://supabase.com/docs/reference/javascript/select) for more information.

Now, we can try to register a new user and check if the user is inserted into the public.user_names table in the Supabase dashboard:

![insert-user1.png](../assets/imgs/insert-user1.png)

![insert-user2.png](../assets/imgs/insert-user2.png)

You can see that the new user is inserted into both the auth.users table and the public.user_names table. And the user_id is successfully linked between these two tables.

