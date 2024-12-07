---
date: 2024-12-06
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Present sending email progress status to user

Continuing with [[2024-12-06_Create-api-endpoint-for-sending-email:-make-sure-inputs-data-to-backend|this topic]], we hope to let users know the current progress of the email sent.

```svelte
<!-- src/lib/components/ContactSection.svelte -->

<script lang="ts">
  import { Button, SectionHeadline } from "$components";

  let contactName = $state("");
  let contactEmail = $state("");
  let contactMessage = $state("");
  let isFormInvalid = $state(false);

  let isEmailSent = $state(false);  // new state to track if email is sent

  async function onSubmit(event: Event) {
//^^^^^
    event.preventDefault();

    if (!contactName || !contactEmail || !contactMessage) {
      isFormInvalid = true;
      return
    }
    await fetch("/api/send-mail", {
  //^^^^^ remember adding `await` keyword to wait for the response, or this fetch will work asynchronously
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

    isEmailSent = true;  // set isEmailSent to true after email is sent

  }

  $effect(() => { if (contactName || !contactName) isFormInvalid = false; })
  $effect(() => { if (contactEmail || !contactEmail) isFormInvalid = false; })
  $effect(() => { if (contactMessage || !contactMessage) isFormInvalid = false; })

</script>

<section class="mt-l">
  <SectionHeadline sectionName="contact-form">Let's talk</SectionHeadline>
  <div class="form-container default-margin mt-m">

     <!-- using #if block to conditionally render the form or success -->
    {#if isEmailSent}
      <p class="success-text">Your message has been sent successfully. I'll get back to you as soon as po ssible.</p>
    {:else}
      <!-- wrap the form in `:else` block to conditionally render the form when email is not sent -->
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
    {/if}
    <div class="form-text">
      <h3 class="bold mb-s">Talk to me about your project</h3>
      <p>
        I'm currently available for freelance work. If you have a project that you want to get started, t
hink you need my help with something or just fancy saying hey, then get in touch. I'll do my best to get 
back to you as soon as possible.
      </p>
    </div>
  </div>
</section>

<style>
  .form-container {
    display: flex;
    justify-content: space-between;
  }
  .form-text {
    width: 39%;
  }
   
  form {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    width: 45%;
  }
  .success-text {
    width: 45%;  /* to match the width of the form above */
  }
  form * {
    font-size: 20px;
    font-family: "Inter Tight", sans-serif;
    font-weight: 500;
    color: black
  }

  /* ... */

</style>

```

Between user clicking the submit button and the email being sent, we can show a loading spinner to indicate the progress:

1. add some delay to simulate the email sending process in our endpoint

```ts
// src/routes/api/send-mail/+server.ts 
import { json } from "@sveltejs/kit";
import type { RequestHandler } from "./$types";

// create a delay function
async function delay(ms: number) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

export const POST: RequestHandler = async ({ request }) => {
  const { name, email, message } = await request.json();
  console.log({ name, email, message });

  await delay(3000); // Simulate a delay

  return json({ emailSentSuccessfully: true });
}
```

2. Then, we can add new state in the frontend component to track the loading status and use it to conditionally render the loading spinner:

```svelte
<!-- src/lib/components/ContactSection.svelte -->

<script lang="ts">
  import { Button, SectionHeadline } from "$components";

  let contactName = $state("");
  let contactEmail = $state("");
  let contactMessage = $state("");
  let isFormInvalid = $state(false);

  let isLoading = $state(true);  // new state to track loading status
  let isEmailSent = $state(false);

  async function onSubmit(event: Event) {
    event.preventDefault();

    if (!contactName || !contactEmail || !contactMessage) {
      isFormInvalid = true;
      return
    }

    isLoading = true;  // set isLoading to true right before sending the email

    await fetch("/api/send-mail", {
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

    isLoading = false  // set isLoading to false right after fetching is done
    isEmailSent = true;
  }

  $effect(() => { if (contactName || !contactName) isFormInvalid = false; })
  $effect(() => { if (contactEmail || !contactEmail) isFormInvalid = false; })
  $effect(() => { if (contactMessage || !contactMessage) isFormInvalid = false; })

</script>

<section class="mt-l">
  <SectionHeadline sectionName="contact-form">Let's talk</SectionHeadline>
  <div class="form-container default-margin mt-m">
    {#if isEmailSent}
      <p class="success-text">Your message has been sent successfully. I'll get back to you as soon as po
ssible.</p>

    <!-- add new block `{:else if isLoading}` to conditionally render the loading spinner -->
    {:else if isLoading}
      <div class="loading-container"> <!-- container for keep whole contain width and arrange spinner and text in a row -->
        <div class="loading-spinner"></div>  <!-- loading spinner, we can simply use CSS and @keyframes to create a spinner animation -->
        <p class="loading-text">Sending...</p>
      </div>

    {:else}
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
    {/if}
    <div class="form-text">
      <h3 class="bold mb-s">Talk to me about your project</h3>
      <p>
        I'm currently available for freelance work. If you have a project that you want to get started, t
hink you need my help with something or just fancy saying hey, then get in touch. I'll do my best to get 
back to you as soon as possible.
      </p>
    </div>
  </div>
</section>

<style>
  .form-container {
    display: flex;
    justify-content: space-between;
  }
  .form-text {
    width: 39%;
  }

  /* add new styles for loading block */
  .loading-container {
    width: 45%;
    display: flex;
    justify-content: flex-start;
    gap: 4px;
  }
  .loading-spinner {
    /* below is a simple spinner using CSS */
    width: 16px;
    height: 16px;
    border: 3px solid rgba(0, 0, 0, 0.2);
    border-top: 3px solid black;
    border-radius: 50%;

    /* make it spin */
    animation: spin 1s linear infinite;
    /*         ^^^^ we will define this keyframes just below */
  }

  .success-text {
    width: 45%;
  }
   
  form {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    width: 45%;
  }
  form * {
    font-size: 20px;
    font-family: "Inter Tight", sans-serif;
    font-weight: 500;
    color: black
  }

  /* ... */

  /* define the `spin` keyframes */
  @keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }

</style>

```
