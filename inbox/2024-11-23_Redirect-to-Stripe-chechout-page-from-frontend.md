---
date: 2024-11-23
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Redirect to Stripe chechout page from frontend

Before, we have implemented the functionality of [[2024-11-23_Pass-some-payment-informations-from-frontend-to-backend|preparing payment information for the backend in Button.svelte]]. Now, after obtaining the session id in the same component [[2024-11-23_Pass-Stripe-session-from-backend-to-Stripe-and-return-session-id-to-frontend|after backend process]], we will use it to redirect the user to the Stripe checkout page as below:

```svelte
<!-- src/lib/components/Button.svelte -->
<script lang="ts">
	import { PUBLIC_PRICE_ID, PUBLIC_STRIPE_KEY } from '$env/static/public';
  //                        ^^^^^^^^^^^^^^^^^ need to create Stripe obj
	import { loadStripe } from '@stripe/stripe-js'; // `npm i @stripe/stripe-js`
  //       ^^^^^^^^^^ we will use it to create Stripe obj (different from Stripe obj in backend)
  //                  which can be used to redirect to the checkout page
	import type { Snippet } from 'svelte';

	interface ButtonProps {
		children: Snippet;
		[key: string]: any;
	}

	let { children, ...props }: ButtonProps = $props();

	async function onclick() {
		const stripe = await loadStripe(PUBLIC_STRIPE_KEY); // create Stripe obj

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

    // get session id from beackend response
		const responseBody = await response.json();
		const { sessionId } = responseBody;

    // use Stripe obj method with session id to redirect user to the checkout page
		await stripe!.redirectToCheckout({ sessionId });
	}
</script>

<button {...props} {onclick}>
	{@render children()}
</button>

<!-- ...omit styles -->
```

Finally, we need to do some [[2024-11-23_Error-handling-for-Stipe-flow|error handling for Stipe flow]]
