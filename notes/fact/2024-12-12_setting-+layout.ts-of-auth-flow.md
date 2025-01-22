---
date: 2024-12-12
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# setting +layout.ts of auth flow

In this topic, we start to implement the fourth step of [[2024-12-09_Svelte-Auth-Request-Flow|Svelte auth Request Flow]], which create a supabase client and use it to get session, user. Finally, pass all the session, user, supabase to the layout.svelte.

We can simply copy paste the code from 
 [Supabase documentation](https://supabase.com/docs/guides/auth/server-side/sveltekit) - step 5 and 1st tap.


```ts
// src/routes/+layout.ts

import { createBrowserClient, createServerClient, isBrowser } from '@supabase/ssr'
import { PUBLIC_SUPABASE_ANON_KEY, PUBLIC_SUPABASE_URL } from '$env/static/public'
import type { LayoutLoad } from './$types'

export const load: LayoutLoad = async ({ data, depends, fetch }) => {
  /**
   * Declare a dependency so the layout can be invalidated, for example, on
   * session refresh.
   */
  depends('supabase:auth')

  // Create a Supabase client based on the environment.
  const supabase = isBrowser()
    ? createBrowserClient(PUBLIC_SUPABASE_URL, PUBLIC_SUPABASE_ANON_KEY, {
        global: {
          fetch,
        },
      })
    : createServerClient(PUBLIC_SUPABASE_URL, PUBLIC_SUPABASE_ANON_KEY, {
        global: {
          fetch,
        },
        cookies: {
          getAll() {
            return data.cookies
          },
        },
      })

  /**
   * It's fine to use `getSession` here, because on the client, `getSession` is
   * safe, and on the server, it reads `session` from the `LayoutData`, which
   * safely checked the session using `safeGetSession`.
   */
  const {
    data: { session },
  } = await supabase.auth.getSession()

  const {
    data: { user },
  } = await supabase.auth.getUser()

  return { session, supabase, user }
}
```
