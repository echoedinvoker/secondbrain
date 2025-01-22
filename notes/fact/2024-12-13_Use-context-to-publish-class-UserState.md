---
date: 2024-12-13
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Use context to publish class UserState

In [[2024-12-13_Create-a-class-to-store-session,-user-and-supabase-client|this topic]], we have created a class to store session, user and supabase client. Now, we will publish this class using context so that we can access it in any component and ensure that we are always using **the same** instance of the class.

```ts
// src/lib/components/state/user-state.svelte.ts

import type { User, Session, SupabaseClient } from "@supabase/supabase-js";
import { getContext, setContext } from "svelte"; // import context functions

interface UserStateProps {
  session: Session | null;
  supabase: SupabaseClient | null;
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
}

// We don't access the above class directly, instead we use the following functions to create and access the instance of the class by context functions

const USER_STATE_KEY = Symbol("USER_STATE");
                    // ^^^^^^ it will return a UNIQUE string value, so that we can use it as a key

export function setUserState(data: UserStateProps) {
  return setContext(USER_STATE_KEY, new UserState(data));
                 // ^^^^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^^ this is the object to store in this context
                 // ^^^^^^^^^^^^^^ context use key to differentiate between different contexts
}

// simply get the instance by key, it ensures that we always get the same instance
export function getUserState() {
  return getContext<ReturnType<typeof setUserState>>(USER_STATE_KEY);
}

```

Because root `+layout.svelte` is the entry point of our frontend app, we should initialize (set) context there.

```svelte
<!-- src/routes/+layout.svelte -->

<script lang="ts">
  import './../app.css'
  import { Header } from '$components'
  import { invalidate } from '$app/navigation'
  import { setUserState } from '$components/state/user-state.svelte'; // import

  let { children, data } = $props()
  const { session, supabase, user } = $derived(data)

  let userState = setUserState(data) // set context with initial data

  // whenever data(session, user, supabase client) changes, update the context
  $effect(() => {
    userState.updateState(data)
  })

  $effect(() => {
    const { data } = supabase.auth.onAuthStateChange((_, newSession) => {
      if (newSession?.expires_at !== session?.expires_at) {
        invalidate('supabase:auth')
      }
    })

    return () => data.subscription.unsubscribe()
  })
</script>

<Header />
{@render children()}

```

Now, we can access the instance of the class in any component with context function `getUserState`.

[[2024-12-13_Render-by-context|Here]] is a case to use the context in the header component to render the header actions based on the user existence.
