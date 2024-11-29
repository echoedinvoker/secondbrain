---
date: 2024-11-23
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Error handling for Stipe flow

```ts
// src/routes/api/checkout/+server.ts 
import { json } from "@sveltejs/kit";
import type { RequestHandler } from "./$types";
import { STRIPE_API_KEY } from '$env/static/private'
import { PUBLIC_FRONTEND_URL } from '$env/static/public'
import Stripe from "stripe";

const stripe = new Stripe(STRIPE_API_KEY)

export const POST: RequestHandler = async ({ request }) => {
    try {
//  ^^^^^ 
      const requestBody = await request.json();
      const { priceId: price, quantity } = requestBody;
      const session = await stripe.checkout.sessions.create({
        payment_method_types: ['card'],
        line_items: [{ price, quantity }],
        mode: 'payment',
        success_url: `${PUBLIC_FRONTEND_URL}/checkout/success`,
        cancel_url: `${PUBLIC_FRONTEND_URL}/checkout/failure`
      })

      return json({ sessionId: session.id })

// handle errors below
    } catch (error: any) {
      return json({ error }, { status: 500 })
      //     ^^^^ so we not really raise the error, but return it as a response to the client
    }
}
```

```svelte
<script lang="ts">
	import { goto } from '$app/navigation'; // use goto to navigate to failure page when onclick fails
	  
	import { PUBLIC_PRICE_ID, PUBLIC_STRIPE_KEY } from '$env/static/public';
	import { loadStripe } from '@stripe/stripe-js';
	import type { Snippet } from 'svelte';

	interface ButtonProps {
		children: Snippet;
		[key: string]: any;
	}

	let { children, ...props }: ButtonProps = $props();

	async function onclick() {
		try {
//  ^^^^^
			const stripe = await loadStripe(PUBLIC_STRIPE_KEY);
      //    ^^^^^^ if this fails, it will go to catch block

			const response = await fetch('/api/checkout', {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json'
				},
				body: JSON.stringify({
					quantity: 1,
					priceId: PUBLIC_PRICE_ID
				})
			});

			const responseBody = await response.json();
      //                                 ^^^^^^^ when api failed, json won't have property sessionId
			const { sessionId } = responseBody;
      //      ^^^^^^^^^ when api failed, it will get undefined
			await stripe!.redirectToCheckout({ sessionId });
      //                                 ^^^^^^^^^ when backend api failure, we won't get sessionId
      //                                           so this will fail and it will go to catch block

// handle errors below
		} catch (error) {
			goto('/checkout/failure');  // redirect to failure page
		}
	}
</script>

<button {...props} {onclick}>
	{@render children()}
</button>

```

