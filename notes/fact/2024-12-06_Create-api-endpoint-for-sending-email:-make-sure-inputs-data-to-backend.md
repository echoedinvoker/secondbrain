---
date: 2024-12-06
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create api endpoint for sending email: make sure inputs data to backend

After [[2024-12-05_Frontend-form-inputs-validation|frontend form inputs validation]] is done, we need to create an api endpoint to send the form data to the backend.


```svelte
<!-- src/lib/components/ContactSection.svelte -->

<script lang="ts">
  import { Button, SectionHeadline } from "$components";

  let contactName = $state("");
  let contactEmail = $state("");
  let contactMessage = $state("");

  let isFormInvalid = $state(false);

  function onSubmit(event: Event) {
    event.preventDefault();

    if (!contactName || !contactEmail || !contactMessage) {
      isFormInvalid = true;
      return
    }

    // Send the form data to the server, here we use fetch api to send the data to the server
    // fetch api is a modern way to make http requests in javascript and it's supported in all modern browsers
    fetch("/api/send-mail", {
    //     ^^^^^^^^^^^^^^ we will create this endpoint just below
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        name: contactName,
        email: contactEmail,
        message: contactMessage,
      }),
    })
  }

  $effect(() => { if (contactName || !contactName) isFormInvalid = false; })
  $effect(() => { if (contactEmail || !contactEmail) isFormInvalid = false; })
  $effect(() => { if (contactMessage || !contactMessage) isFormInvalid = false; })

</script>

<section class="mt-l">
  <SectionHeadline sectionName="contact-form">Let's talk</SectionHeadline>
  <div class="form-container default-margin mt-m">
    <form onsubmit={onSubmit}>
      <input
        class="text-input mb-m"
        class:input-error={isFormInvalid && !Boolean(contactName.length)}
        placeholder="Your Name"
        bind:value={contactName}
      />
      <input
        class="text-input mb-m"
        class:input-error={isFormInvalid && !Boolean(contactEmail.length)}
        placeholder="Your Email"
        bind:value={contactEmail}
      />
      <textarea
        class:input-error={isFormInvalid && !Boolean(contactMessage.length)}
        placeholder="Tell we what's up."
        bind:value={contactMessage}></textarea>
      <Button>Submit</Button>
    </form>
    <div class="form-text">
      <h3 class="bold mb-s">Talk to me about your project</h3>
      <p>
        I'm currently available for freelance work. If you have a project that you want to get started, think you need my help w
ith something or just fancy saying hey, then get in touch. I'll do my best to get back to you as soon as possible.
      </p>
    </div>
  </div>
</section>
```

```bash
~/D/g/s/s/dev-portfolio > tree src/
src/
...(omit)
└── routes
    ├── api  # create api folder
    │   └── send-mail  # folder under `routes/api` will become endpoint path
    │       └── +server.ts  # `+server.ts` to handle endpoint logic
    ├── +layout.svelte
    ├── +page.svelte
    ├── +page.ts
    └── work
        └── [slug]
            ├── +page.svelte
            └── +page.ts

```

```ts
// src/routes/api/send-mail/+server.ts 

import { json } from "@sveltejs/kit";
import type { RequestHandler } from "./$types";

export const POST: RequestHandler = async ({ request }) => {
  const { name, email, message } = await request.json();
  console.log({ name, email, message });

  return json({ emailSentSuccessfully: true });
}

```

```bash
~/D/g/s/s/dev-portfolio > npm run dev

> dev-portfolio@0.0.1 dev
> vite dev


  VITE v5.4.11  ready in 680 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help


# fill in the contact form and click submit button...

10:18:49 AM [vite] page reload src/routes/api/send-mail/+server.ts
{ name: 'matt', email: 'matt@mail.com', message: 'some messages...' }  # got correct data in the backend

# frontend should also got response as below
{ emailSentSuccessfully: true }

```
