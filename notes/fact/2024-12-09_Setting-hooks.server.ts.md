---
date: 2024-12-09
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Setting hooks.server.ts

This topic is to setup the **2nd step** of the [[2024-12-09_Svelte-Auth-Request-Flow|Svelte & Supabase Auth Flow]].

We will just stick to the [Supabase documentation](https://supabase.com/docs/guides/auth/server-side/sveltekit), below we just copy paste the code from `3. Set up server-side hooks` to our project:

```ts
// src/hooks.server.ts (new file)

import { createServerClient } from '@supabase/ssr'
import { type Handle, redirect } from '@sveltejs/kit'
import { sequence } from '@sveltejs/kit/hooks'

import { PUBLIC_SUPABASE_URL, PUBLIC_SUPABASE_ANON_KEY } from '$env/static/public'

const supabase: Handle = async ({ event, resolve }) => {
  /**
   * Creates a Supabase client specific to this server request.
   *
   * The Supabase client gets the Auth token from the request cookies.
   */
  event.locals.supabase = createServerClient(PUBLIC_SUPABASE_URL, PUBLIC_SUPABASE_ANON_KEY, {
    cookies: {
      getAll: () => event.cookies.getAll(),
      /**
       * SvelteKit's cookies API requires `path` to be explicitly set in
       * the cookie options. Setting `path` to `/` replicates previous/
       * standard behavior.
       */
      setAll: (cookiesToSet) => {
        cookiesToSet.forEach(({ name, value, options }) => {
          event.cookies.set(name, value, { ...options, path: '/' })
        })
      },
    },
  })

  /**
   * Unlike `supabase.auth.getSession()`, which returns the session _without_
   * validating the JWT, this function also calls `getUser()` to validate the
   * JWT before returning the session.
   */
  event.locals.safeGetSession = async () => {
    const {
      data: { session },
    } = await event.locals.supabase.auth.getSession()
    if (!session) {
      return { session: null, user: null }
    }

    const {
      data: { user },
      error,
    } = await event.locals.supabase.auth.getUser()
    if (error) {
      // JWT validation has failed
      return { session: null, user: null }
    }

    return { session, user }
  }

  return resolve(event, {
    filterSerializedResponseHeaders(name) {
      /**
       * Supabase libraries use the `content-range` and `x-supabase-api-version`
       * headers, so we need to tell SvelteKit to pass it through.
       */
      return name === 'content-range' || name === 'x-supabase-api-version'
    },
  })
}

// check the result of above, and redirect the user based on their auth status
const authGuard: Handle = async ({ event, resolve }) => {
  const { session, user } = await event.locals.safeGetSession()
  event.locals.session = session
  event.locals.user = user

  if (!event.locals.session && event.url.pathname.startsWith('/private')) {
    redirect(303, '/login')
                // ^^^^^^ default value is `/auth`, we replace it with `/login` which fits our project
  }

  if (event.locals.session && ['/register', 'login'].includes(event.url.pathname) {
                           // ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ default only check if `/auth`, we need to change it to fit our project
    redirect(303, '/private/dashboard')
                // ^^^^^^^^^^^^^^^^^^ default value is only `private` but we use `/private/dashboard` in our project
  }

  return resolve(event)
}

export const handle: Handle = sequence(supabase, authGuard) // this is just like the setup of middleware in node.js,
                                                            // each request to the server will go through the sequence of supabase and authGuard:
                                                                                                    // supabase to generate client and session
                                                                                                    // then authGuard to redirect user by checking the result of supabae
                

```

Right now, there are still lots of Typescript errors in the code above, we will fix them in the next step `4. Create TypeScript definitions` of the documentation:

Just copy paste codes from the documentation to our project (overwrite the existing `src/app.d.ts`):

```ts
// src/app.d.ts 

import type { Session, SupabaseClient, User } from '@supabase/supabase-js'

declare global {
  namespace App {
    // interface Error {}
    interface Locals {
      supabase: SupabaseClient
      safeGetSession: () => Promise<{ session: Session | null; user: User | null }>
      session: Session | null
      user: User | null
    }
    interface PageData {
      session: Session | null
    }
    // interface PageState {}
    // interface Platform {}
  }
}

export {}

```

Then, the errors in `src/hooks.server.ts` should be gone.

