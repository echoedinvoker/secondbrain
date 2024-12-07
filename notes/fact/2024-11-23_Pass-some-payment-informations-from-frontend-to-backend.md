---
date: 2024-11-23
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Pass some payment informations from frontend to backend

Now we start to implement the first step of [[2024-11-22_Stripe-Integration-flow|Stripe Integration flow]]:

```svelte
<!-- src/lib/components/Button.svelte  -->
<script lang="ts">
	import { PUBLIC_PRICE_ID } from '$env/static/public'; 
  //       ^^^^^^                 ^^^^^^^^^^^^^^^^^^^^ if change `public` to `private` will get error
  //         ^                                         frontend can't access to `private` one
  //         ^
  //       only PUBLIC_ prefix is allowed import from $env/static/public

	import type { Snippet } from 'svelte';

	interface ButtonProps {
		children: Snippet;
		[key: string]: any;
	}

	let { children, ...props }: ButtonProps = $props();


  // create a handler to send the payment information to the backend
	async function onclick() {
		const response = await fetch('/api/checkout', {
    //                            ^^^^^^^^^^^^^ relative path means our backend api
			method: 'POST',
			headers: {
				'Content-Type': 'application/json'
        //               ^^^^^^^^^^^^^^^^
			},
      // we use json body to carry the payment information to the backend
			body: JSON.stringify({
				quantity: 1,
				priceId: PUBLIC_PRICE_ID
			})
		});

		const session = await response.json();
    //                            ^^^^^^^ method to unpack json body of response from backend

		console.log(session); // { success: true } printed in console
	}
</script>

<button {...props} {onclick}>
              <!-- ^^^^^^^^^ -->
	{@render children()}
</button>

<!-- ...omit styles -->
```

This topic is focused on how to pass some payment information from frontend to backend. So now we just create a dummy api to receive the payment information from the frontend:

```ts
// src/routes/api/checkout/+server.ts 

import { json } from "@sveltejs/kit"; // method to response with json body

export async function POST({ request }) {

  const requestBoty = await request.json();
  //                               ^^^^^^^ method to unpack json body from the frontend

  console.log(requestBoty);  // { quantity: 1, priceId: 'price_1JdFh2LX7Dh8Y6Z5z1z1z1z1' }  printed in terminal

  return json({ success: true });
}

```
