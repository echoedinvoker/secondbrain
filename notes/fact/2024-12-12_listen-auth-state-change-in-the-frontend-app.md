---
date: 2024-12-12
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# listen auth state change in the frontend app

In this topic, we want to do the fifth step of [[2024-12-09_Svelte-Auth-Request-Flow|Svelte Auth Request Flow]], which is to listen to the authentication state change in the frontend app, if changed, it should re-do the whole auth flow to update the session, user, and Supabase client instance.

Same, [Supabase documentation](https://supabase.com/docs/guides/auth/server-side/sveltekit) provides the script for us to directly use. (step 6)

But it is for old SvelteKit, so we need to do some modification.

**+layout.svelte codes provides by Supabase**

```svelte
<script>
  import { invalidate } from '$app/navigation'
  import { onMount } from 'svelte'

  export let data;  // old way 
  $: ({ session, superbase }) = data); // old way 


  onMount(() => {  // old way 
    const { data } = supabase.auth.onAuthStateChange((_, newSession) => {
      if (newSession?.expires_at !== session?.expires_at) {
        invalidate('supabase:auth')
      }
    })

    return () => data.subscription.unsubscribe()
  })
</script>

<slot /> <!-- old way -->

```

Our +layout.svelte already has a little bit of code, we merge above code into it with new SvleteKit syntax as below:

```svelte
<!-- src/routes/+layout.svelte -->

<script lang="ts">
  import './../app.css'
  import { Header } from '$components'
  import { invalidate } from '$app/navigation' // import invalidate from SvelteKit

  let { children, data } = $props()
               // ^^^^ get data from load function (layout.ts)
  const { session, supabase, user } = $derived(data)
                                   // ^^^^^^^^ use rune $derived to destructure the data

  $effect(() => { // just replace onMount with $effect

    const { data } = supabase.auth.onAuthStateChange((_, newSession) => {
                                  // ^^^^^^^^^^^^^^^^^^ supabase client provides method to listen auth state change
                                        
      // so when auth state change, we check if the session is expired, if yes, invalidate the auth
      // if auth is invalidated, SvelteKit will re-do the whole auth flow to update the session, user, and supabase client instance
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
