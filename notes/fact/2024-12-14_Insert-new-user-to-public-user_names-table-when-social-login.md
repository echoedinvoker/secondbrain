---
date: 2024-12-14
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Insert new user to public user_names table when social login

In [[2024-12-14_Insert-new-user-to-public-user_names-table-when-registering|this topic]], we implemented a way to insert a new user to the `user_names` table when they register with email and password.

But there is another situation where we also need to insert a new user to the `user_names` table when they register with social login.

Let's check the from action used for social login:

```ts
// src/routes/login/+page.server.ts 

export const actions = {
  signInWithPassword: async ({ request, locals: { supabase } }) => { ... },

  // this is the action for google login
  googleLogin: async ({ request, locals: { supabase } }) => {
    const { data, error } = await supabase.auth.signInWithOAuth({
      provider: 'google',
      options: {
        redirectTo: `${PUBLIC_FRONTEND_URL}/auth/callback`,
        // when the login is successful, Supabase will hit above endpoint
        // so that's the real place where we need to insert the user to the user_names table
      }
    })

    if (error) return fail(400, { message: "Something went wrong in the google login" });

    throw redirect(303, data.url);
  }
} satisfies Actions;

```

```ts
// src/routes/auth/callback/+server.ts

import { redirect, type RequestHandler } from "@sveltejs/kit";

export const GET: RequestHandler = async ({ url, locals: { supabase } }) => {
  const code = url.searchParams.get('code');

  if (code) await supabase.auth.exchangeCodeForSession(code);

  const sessionData = await supabase.auth.getSession();

  if (!sessionData.data.session) return new Response("Session not found", { status: 400 });

  // we need to insert the user to the user_names table here
  const userId = sessionData.data.session.user.id;
  const userName = sessionData.data.session.user.user_metadata.name;
  await supabase.from('user_names').insert([
    {
      user_id: userId,
      name: userName
    }
  ])

  throw redirect(303, "/private/dashboard");
}

```

But there is a problem with the above code. If a user logs in with google but he has already registered by Google login before, then we will get an error because we are trying to insert the same user again.

To solve this problem, we need to check if the user is already in the `user_names` table before inserting.

```ts
// src/routes/auth/callback/+server.ts

import { redirect, type RequestHandler } from "@sveltejs/kit";

export const GET: RequestHandler = async ({ url, locals: { supabase } }) => {
  const code = url.searchParams.get('code');

  if (code) await supabase.auth.exchangeCodeForSession(code);

  const sessionData = await supabase.auth.getSession();

  if (!sessionData.data.session) return new Response("Session not found", { status: 400 });

  const userId = sessionData.data.session.user.id;
  const userName = sessionData.data.session.user.user_metadata.name;

  // check if the user is already in the user_names table
  const { data: existingUser, error: selectError} = await supabase
    .from('user_names').select('name').eq('user_id', userId).single();
    //                                                      ^^^^^^^^^ if there is no record matching the user_id,
    //                                                                .single() will throw error with code PGRST116

  if (selectError && selectError.code !== 'PGRST116')  // consider fetching error situation (exluding PGRST116, not found case)
    return new Response("Failed to check existing user", { status: 500 });

  if (!existingUser) // if the user is not in the user_names table, insert it
    await supabase.from('user_names').insert([
      {
        user_id: userId,
        name: userName
      }
    ])

  throw redirect(303, "/private/dashboard");
}

```
