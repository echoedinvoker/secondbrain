---
date: 2024-12-14
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Logout the Book Nest

Logout is an operation that interacts with Supabase, so we should write it under the class UserState as a method:

```ts
// src/lib/components/state/user-state.svelte.ts

import type { User, Session, SupabaseClient } from "@supabase/supabase-js";
import { getContext, setContext } from "svelte";

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

  // Add this method
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

Then, we can use this method on the button in the Header component:

```svelte
<!-- src/lib/components/Header.svelte  -->

<script lang="ts">
  import bookNextLogo from '$assets/app-logo.svg';
  import { Button } from '$components';
  import { getUserState } from './state/user-state.svelte';

  let userState = getUserState()
  let { user } = $derived(userState)

  // Add this function
  function onclick() { userState.logout() }
</script>

<header>
  <a href="/">
    <img class="logo " src={bookNextLogo} alt="Go to home">
  </a>
  <nav>
    {#if user}
      <ul>
        <li>
          <p>{user.email}</p>
        </li>
        <li>
          <Button isMenu={true} {onclick}>Logout</Button> <!-- Add onclick here -->
        </li>
      </ul>
    {:else}
      <ul>
        <li>
          <Button isMenu href="/register">Create account</Button>
        </li>
        <li>
          <Button isMenu isSecondary href="/login">Login</Button>
        </li>
      </ul>
    {/if}
  </nav>
</header>
```

Now, we can try to logout from the Book Nest application.

But we found that the user is still logged in after clicking the logout button. We need to do a random navigation to let user logout, that's because when we logout, it triggers a listener that invalidates the session in below code:

```svelte
<!-- src/routes/+layout.svelte -->

<script lang="ts">
  import './../app.css'
  import { Header } from '$components'
  import { invalidate } from '$app/navigation'
    import { setUserState } from '$components/state/user-state.svelte';

  let { children, data } = $props()
  const { session, supabase, user } = $derived(data)

  let userState = setUserState(data)

  $effect(() => {
    userState.updateState(data)
  })

  // this will be triggered when user click logout button
  $effect(() => {
    const { data } = supabase.auth.onAuthStateChange((_, newSession) => {
      if (newSession?.expires_at !== session?.expires_at) {
        invalidate('supabase:auth')  // this invalidates the load function below
      }
    })

    return () => data.subscription.unsubscribe()
  })
</script>

<Header />
{@render children()}

```
That invalidates the load function in the layout in the below code:

```ts
// src/routes/+layout.ts 

import { createBrowserClient, createServerClient, isBrowser } from '@supabase/ssr'
import { PUBLIC_SUPABASE_ANON_KEY, PUBLIC_SUPABASE_URL } from '$env/static/public'
import type { LayoutLoad } from './$types'

export const load: LayoutLoad = async ({ data, depends, fetch }) => {

  depends('supabase:auth')  // it means when this dependency is invalidated, this function will be called again
                            // but load function can only be called when the page is loaded
                            // that's why we need to do a random navigation to let user logout

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

  const {
    data: { session },
  } = await supabase.auth.getSession()

  const {
    data: { user },
  } = await supabase.auth.getUser()

  return { session, supabase, user }
}
```

So, we can simply add a navigation process after the logout in the logout method of the UserState class:

```ts
// src/lib/components/state/user-state.svelte.ts

import { goto } from "$app/navigation"; // import goto from sveltekit
import type { User, Session, SupabaseClient } from "@supabase/supabase-js";
import { getContext, setContext } from "svelte";

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

  async logout() {
    await this.supabase?.auth.signOut();
    goto("/login");  // simply add a navigation process after the logout to make sure the user is logged out immediately
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

