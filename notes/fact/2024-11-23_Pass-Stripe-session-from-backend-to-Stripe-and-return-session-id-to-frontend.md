---
date: 2024-11-23
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Pass Stripe session from backend to Stripe and return session id to frontend

```ts
// src/routes/api/checkout/+server.ts 
import { json } from "@sveltejs/kit";
import type { RequestHandler } from "./$types";
import { STRIPE_API_KEY } from '$env/static/private' // from private env, not public
//       ^^^^^^^^^^^^^^                              // env without PUBLIC_ prefix is private in .env
//       ^ we need secret key to create Stripe session obj

import Stripe from "stripe"; // `npm i stripe` 

const stripe = new Stripe(STRIPE_API_KEY)  // create stripe obj with secret key

export const POST: RequestHandler = async ({ request }) => {
  const requestBody = await request.json();
  const { priceId: price, quantity } = requestBody;

  // use stripe obj to create session, it will pass to Stripe automatically, and return session information
  const session = await stripe.checkout.sessions.create({

    // there are many options here, we can check Stripe API doc
    payment_method_types: ['card'],
    line_items: [{ price, quantity }],
    //             ^^^^^^^^^^^^^^^  these informations comes from frontend,
    //                              price means price id, quantity means how many items
    mode: 'payment',

    // these urls are for redirect after checkout is done, it will be redirected from Stripe checkout page to our project
    success_url: 'http://localhost:5173/checkout/success',
    cancel_url: 'http://localhost:5173/checkout/failure'
    //           ^^^^^^^^^^^^^^^^^^^^^ this is meaningless when we deploy our project to production,
    //                                 so better to make it to be env variable
  })

  return json({ sessionId: session.id })
  //            ^^^^^^^^^ frontend need session id to redirect user to Stripe checkout page
}
```

Make redirect urls to be env variable:

```bash
# .env
PUBLIC_STRIPE_KEY=pk_test_51OOKN1DJ2r9ILm4nrekokKkOaLyw21ele60e5ni6y2qfXioNf5b44YMP5FGiVtvEXywwnaPfeqC3CsO1pCE7KfIu00aQfaIf8G
STRIPE_API_KEY=sk_test_51OOKN1DJ2r9ILm4n1Bwd5zjOiv48HT12LoFv7Y2fRxVyVYfz68K26r4pcz0FKvzm0Hqqe1STwV66CAK1W8UIhrSM00QQIOdzde
PUBLIC_PRICE_ID=price_1QNqyIDJ2r9ILm4n1laYZXHh
PUBLIC_FRONTEND_URL=http://localhost:5173  # add this line
```

```ts
// src/routes/api/checkout/+server.ts 
import { json } from "@sveltejs/kit";
import type { RequestHandler } from "./$types";
import { STRIPE_API_KEY } from '$env/static/private'
import { PUBLIC_FRONTEND_URL } from '$env/static/public' // import public env: PUBLIC_FRONTEND_URL
import Stripe from "stripe";

const stripe = new Stripe(STRIPE_API_KEY)

export const POST: RequestHandler = async ({ request }) => {
  const requestBody = await request.json();
  const { priceId: price, quantity } = requestBody;
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ['card'],
    line_items: [{ price, quantity }],
    mode: 'payment',

    // replace hardcoded domain with env variable
    success_url: `${PUBLIC_FRONTEND_URL}/checkout/success`,
    //            ^^^^^^^^^^^^^^^^^^^^^^
    cancel_url: `${PUBLIC_FRONTEND_URL}/checkout/failure`
    //           ^^^^^^^^^^^^^^^^^^^^^^
  })

  return json({ sessionId: session.id })
}
```
Then, the frontend can use this session id to redirect user to Stripe checkout page.
