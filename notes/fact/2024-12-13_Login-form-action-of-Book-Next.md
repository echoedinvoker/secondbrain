---
date: 2024-12-13
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Login form action of Book Next

```bash
~/D/g/s/s/book-nest > tree src/
src/
├── app.css
├── app.d.ts
├── app.html
├── assets
├── hooks.server.ts
├── lib
└── routes
    ├── +layout.server.ts
    ├── +layout.svelte
    ├── +layout.ts
    ├── login
    │   ├── +page.server.ts # paste
    │   └── +page.svelte # form method="post" will trigger form action in the same directory
    ├── +page.svelte
    └── register
        ├── +page.server.ts  # copy 
        └── +page.svelte

```

The form action required for login is very similar to register, except that it does not require entering name and passwordConfirm. So we just need to remove the relevant parts.

```ts
// src/routes/login/+page.server.ts 

import type { Actions } from './$types';
import { fail, redirect } from '@sveltejs/kit';

interface ReturnObject {
  success: boolean;
  errors: string[];
}

export const actions = {
  default: async ({ request, locals: { supabase } }) => {

    const formData = await request.formData();

    // const name = formData.get('name') as string;
    const email = formData.get('email') as string;
    const password = formData.get('password') as string;
    // const passwordConfirm = formData.get('passwordConfirm') as string;

    const returnObject: ReturnObject = {
      success: true,
      errors: [],
    };

    // if (name.length < 3) returnObject.errors.push('Name must be at least 3 characters long');
    if (!email.length) returnObject.errors.push('Email is required');
    if (!password.length) returnObject.errors.push('Password is required');
    // if (password !== passwordConfirm) returnObject.errors.push('Passwords do not match');
    
    if (returnObject.errors.length) returnObject.success = false;

    const { data, error } = await supabase.auth.signInWithPassword({ email, password })
    //                                          ^^^^^^^^^^^^^^^^^^ replace signUp with signInWithPassword

    if (error || !data.user) {
      returnObject.success = false;
      return fail(400, returnObject as any)
    }

    redirect(303, "/private/dashboard");


  }
} satisfies Actions;

```


