---
date: 2024-12-15
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Generate types from supabase

Like Sanity, we can also generate types from Supabase. We can use these types to make our project more type safe or save time to write some types by hand.

```bash
#(maybe you need to install supabase cli first, I'm not sure...)

npx supabase login
#Here is your login link in case browser did not open https://supabase.com/dashboard/cli/login?session_id=0e5bbb8e-1f22-4c95-8514-807e6a390203&token_name=cli_mattc@#matt_1734250561&public_key=04e532a21a7de327beedf29e0e5c2bce9b04924c146343c6b3c281b94528887ceef9414e5bb2e15560890f92bd115ab4e2f4ba76c467847d363c862431b55703a1
#(it will open your browser automatically and ask for permission)

#Enter your verification code: c83c8abc
                              #^^^^^^^^ This is the code you will get on the browser
#Token cli_mattc@matt_1734250561 created successfully.
#You are now logged in. Happy coding!

npx supabase init
#Generate VS Code settings for Deno? [y/N] 
#Generate IntelliJ Settings for Deno? [y/N] 
#Finished supabase init.
npx supabase gen types typescript --project-id oqpbjpibdgssyuqwqbts > database.types.ts
#                                              ^^^^^^^^^^^^^^^^^^^^ your supabase project id 
mv database.types.ts src/lib/types/
rm -rf supabase # this folder is useless, you can delete it
```

We got the project id here:

![prj-id.png](../assets/imgs/prj-id.png)
Then, we generate a file with types as below:

```ts
// src/lib/types/database.types.ts 

export type Json =
  | string
  | number
  | boolean
  | null
  | { [key: string]: Json | undefined }
  | Json[]

export type Database = {
  //        ^^^^^^^^ We can put this type to SupabaseClient<Database> to get the full type of the database
  //                 createServerClient and createBrowserClient can also be used to get the full type of the database
  //                 this is optional, but it can make your project more type safe
  public: {
    Tables: {
      books: {
        Row: {

          // We can copy codes here to create a new type `Book` in our component,
          // it saves us some time to write types by hand
          author: string | null
          cover_image: string | null
          created_at: string
          description: string | null
          finished_reading_on: string | null
          genre: string | null
          id: number
          rating: number | null
          started_reading_on: string | null
          title: string
          user_id: string

        }
        Insert: {
          author?: string | null
          cover_image?: string | null
          created_at?: string
          description?: string | null
          finished_reading_on?: string | null
          genre?: string | null
          id?: number
          rating?: number | null
          started_reading_on?: string | null
          title: string
          user_id?: string
        }
        Update: {
          author?: string | null
          cover_image?: string | null
          created_at?: string
          description?: string | null
          finished_reading_on?: string | null
          genre?: string | null
          id?: number
          rating?: number | null
          started_reading_on?: string | null
          title?: string
          user_id?: string
        }
        Relationships: []
      }
      user_names: {
        Row: {
          created_at: string
          id: number
          name: string | null
          user_id: string | null
        }
        Insert: {
          created_at?: string
          id?: number
          name?: string | null
          user_id?: string | null
        }
        Update: {
          created_at?: string
          id?: number
          name?: string | null
          user_id?: string | null
        }
        Relationships: []
      }
    }
    Views: {
      [_ in never]: never
    }
    Functions: {
      [_ in never]: never
    }
    Enums: {
      [_ in never]: never
    }
    CompositeTypes: {
      [_ in never]: never
    }
  }
}

type PublicSchema = Database[Extract<keyof Database, "public">]

//...(omit)
```

Add the type `Database` to existing `SupabaseClient` type in `src/app.d.ts`:

```ts
~/D/g/s/s/book-nest > cat src/app.d.ts 
import type { Database } from '$lib/types/database.types'; // import
import type { Session, SupabaseClient, User } from '@supabase/supabase-js'

declare global {
  namespace App {
    // interface Error {}
    interface Locals {
      supabase: SupabaseClient<Database>
      //                      ^^^^^^^^^^ added, it make this type more complete
      safeGetSession: () => Promise<{ session: Session | null; user: User | null }>
      session: Session | null
      user: User | null
    }
    interface PageData {
      session: Session | null
    }
  }
}

export {}


// src/lib/components/state/user-state.svelte.ts

import type { Database } from "$lib/types/database.types";  // import
import type { User, Session, SupabaseClient } from "@supabase/supabase-js";
import { getContext, setContext } from "svelte";

interface UserStateProps {
  session: Session | null;
  supabase: SupabaseClient<Database> | null;
  //                      ^^^^^^^^^^ added, it make this type more complete
  user: User | null;
}

export class UserState {
  session = $state<Session | null>(null);
  supabase = $state<SupabaseClient | null>(null);
  user = $state<User | null>(null);

  constructor(data: UserStateProps) {
    this.updateState(data);
  }

  updateState({ session, supabase, user }: UserStateProps) {
    this.session = session;
    this.supabase = supabase;
    this.user = user;
  }

  async logout() {
    await this.supabase?.auth.signOut();
  }
}

const USER_STATE_KEY = Symbol("USER_STATE");

export function setUserState(data: UserStateProps) {
  return setContext(USER_STATE_KEY, new UserState(data));
}

export function getUserState() {
  return getContext<ReturnType<typeof setUserState>>(USER_STATE_KEY);
}
```

Add the type `Database` to methods which creating supabase client in different files:

```ts
// src/routes/+layout.ts

import { createBrowserClient, createServerClient, isBrowser } from '@supabase/ssr'
import { PUBLIC_SUPABASE_ANON_KEY, PUBLIC_SUPABASE_URL } from '$env/static/public'
import type { LayoutLoad } from './$types'
import type { Database } from '$lib/types/database.types' // import

export const load: LayoutLoad = async ({ data, depends, fetch }) => {

  depends('supabase:auth')

  const supabase = isBrowser()
    ? createBrowserClient<Database>(PUBLIC_SUPABASE_URL, PUBLIC_SUPABASE_ANON_KEY, {
      //                 ^^^^^^^^^^ added, it make this type more complete
        global: {
          fetch,
        },
      })
    : createServerClient<Database>(PUBLIC_SUPABASE_URL, PUBLIC_SUPABASE_ANON_KEY, {
      //                ^^^^^^^^^^ added, it make this type more complete
        global: {
          fetch,
        },
        cookies: {
          getAll() {
            return data.cookies
          },
        },
      })

//...(omit)


// src/hooks.server.ts 

import { createServerClient } from '@supabase/ssr'
import { type Handle, redirect } from '@sveltejs/kit'
import { sequence } from '@sveltejs/kit/hooks'

import { PUBLIC_SUPABASE_URL, PUBLIC_SUPABASE_ANON_KEY } from '$env/static/public'
import type { Database } from '$lib/types/database.types'  // import

const supabase: Handle = async ({ event, resolve }) => {
  event.locals.supabase = createServerClient<Database>(PUBLIC_SUPABASE_URL, PUBLIC_SUPABASE_ANON_KEY, {
  //                                        ^^^^^^^^^^ added, it make this type more complete
    cookies: {
      getAll: () => event.cookies.getAll(),
      setAll: (cookiesToSet) => {
        cookiesToSet.forEach(({ name, value, options }) => {
          event.cookies.set(name, value, { ...options, path: '/' })
        })
      },
    },
  })

//...(omit)

```
