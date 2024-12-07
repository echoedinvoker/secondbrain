---
date: 2024-11-24
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# How to secure your stripe webhook

In [[2024-11-24_Send-email-through-SendGrid|this topic]], we create a API endpoint to accept incoming webhooks from SendGrid and send emails.

But in fact, anyone can send a POST request to our API endpoint, it's not secure. So we better to check the request SendGrid signature to make sure the request is from SendGrid.


## Steps to Secure Your Webhook

**1. Retrieve Your Webhook Secret:**
   - On your Stripe dashboard, go to Developers > Webhooks, and select the endpoint you’re using.
   - Copy the webhook signing secret and store it as an environment variable (STRIPE_WEBHOOK_SECRET).


**2. Verify the Signature in our endpoint:**
   - Ensure your code checks the `stripe-signature` header and validates the event payload.

In order to do that, we have to bring in the Stripe backend package, the STRIPE_API_KEY & the STRIPE_WEBHOOK_SECRET importing the Stripe backend package and the secret Stripe API key like so:

```ts
import { SENDGRID_API_KEY, STRIPE_WEBHOOK_SECRET, STRIPE_API_KEY } from "$env/static/private";
import Stripe from "stripe";
 
const stripe = new Stripe(STRIPE_API_KEY);

```

Inside of our post request handler, we can now grab the stripe signature from the headers like so:

```ts
const stripeSignature = request.headers.get('stripe-signature');

```


To validate the Stripe signature, we use the `constructEvent` function from stripe, which requires the **raw request body** to verify authenticity.

To obtain the raw body, call `request.text()` and pass it directly to constructEvent, which will verify the request. If it cannot be verified, it will throw an error. Otherwise it will return a parsed object of the request that we can work with.

**Note:** `request.text()` or `request.json()` can only be called once on the request object, as it consumes the body. Therefore, we have save the result of constructEvent function to a variable, to be able to get the customerEmail and customerName from it.

```ts
  const body = await request.text(); // Get the raw body
  const signature = request.headers.get("stripe-signature") || "";
 
  try {
    const stripeEvent = stripe.webhooks.constructEvent(
      body,
      signature,
      STRIPE_WEBHOOK_SECRET
    );
 
    const customerEmail = stripeEvent.data.object.customer_details.email;
    const customerName = session.customer_details.name;
  } catch (err) {
    return json({ error: 'Webhook signature verification failed' }, { status: 400 });
  }

```


I also pushed the whole code to another branch in our course repository here:
[https://github.com/Nickfis/ebook_landing_course/blob/stripe-verification/src/routes/api/purchase-confirmation/%2Bserver.js]
