---
date: 2024-12-15
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Better way to refresh user state when logout

In [[2024-12-14_Logout-the-Book-Nest|this topic]], we put an $effect to `+layout.svelte` to refresh the user state when the user logs out as below:

```svelte
<!-- src/routes/+layout.svelte  -->

<script lang="ts">
  import './../app.css'
  import { Header } from '$components'
  import { invalidate } from '$app/navigation'
    import { setUserState } from '$components/state/user-state.svelte';

  let { children, data } = $props()
  const { session, supabase, user } = $derived(data)

  let userState = setUserState(data)

  // we put this effect to refresh the user state when the user logs out
  // this way needs navigation to refresh the user state
  // and in old SvelteKit versions, it has chances to infinite loop
  $effect(() => {
    userState.updateState(data)
  })

  $effect(() => {
    const { data } = supabase.auth.onAuthStateChange((_, newSession) => {
    //                            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    //                            why not just use this listener that supabase provides?
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

As commented in the code, we can use the listener that supabase provides to refresh the user state when the user logs out instead of using another $effect. 

```svelte
~/D/g/s/s/book-nest > cat src/routes/+layout.svelte
<script lang="ts">
  import './../app.css'
  import { Header } from '$components'
  import { invalidate } from '$app/navigation'
    import { setUserState } from '$components/state/user-state.svelte';

  let { children, data } = $props()
  const { session, supabase, user } = $derived(data)

  let userState = setUserState(data)

  // remove $effect

  $effect(() => {
    const { data } = supabase.auth.onAuthStateChange((_, newSession) => {
      
      // update the user state here
      userState.updateState({
        session: newSession,
        supabase,
        user: newSession?.user || null,
      })

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

Now, because the refresh of the user state is inside the listener, it will be updated when the user logs out without the need for navigation. 

```ts
~/D/g/s/s/book-nest > cat src/lib/components/state/user-state.svelte.ts
// import { goto } from "$app/navigation";
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
    // goto("/login");    // we don't need any navigation to trigger the updateState now
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

And, the infinite loop issue is also resolved. (for some old SvelteKit versions)

