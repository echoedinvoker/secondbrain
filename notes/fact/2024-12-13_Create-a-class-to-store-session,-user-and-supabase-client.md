---
date: 2024-12-13
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create a class to store session, user and supabase client

We have completed steps 1-4 of the [[2024-12-09_Svelte-Auth-Request-Flow|Svelte Auth Flow]] and used the Supabase client instance obtained during the process to perform registration and login form actions.

However, we have not yet completed step 5 of the auth flow: using context to make the session, user, and Supabase client accessible to all frontend components.

Therefore, the first step is to create a class to store session, user, and Supabase client:

```ts
// src/lib/components/state/user-state.svelte.ts   we create a ts file to store session, user, and Supabase client by a class

import type { User, Session, SupabaseClient } from "@supabase/supabase-js";  // Supabase library provides types for session, user, and SupabaseClient

interface UserStateProps {
  session: Session | null;
  supabase: SupabaseClient | null;
  user: User | null;
}

// create a class to store session, user, and Supabase client
export class UserState {
  session = $state<Session | null>(null);
         // ^^^^^^ $state is important because frontend ui will be updated when the value changes
  supabase = $state<SupabaseClient | null>(null);
  user = $state<User | null>(null);

  constructor(data: UserStateProps) {
    this.updateState(data); // just reusing the updateState method to reduce code duplication
  }

  updateState({ session, supabase, user }: UserStateProps) {
    this.session = session;
    this.supabase = supabase;
    this.user = user;
  }

  // any actions that interact with the session, user, and Supabase client should be as methods of this class!

}

```

Now, if we use this class in our components, we will create a lots of instances of it. But that's not the result we want. We want to create a single instance of this class and use it throughout our application. 

So we will use [[2024-12-13_Use-context-to-publish-class-UserState|Svelte's context API]] to create a single instance of this class and make it available to all components. 
