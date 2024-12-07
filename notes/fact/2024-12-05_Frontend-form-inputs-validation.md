---
date: 2024-12-05
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Frontend form inputs validation

Based on the codes of [[2024-12-05_Tracking-input-value-in-Svelte|this topic]], we want to add frontend validation to the form inputs. And error styling should be added to the input fields when the user submits are invalid:

```svelte
<!-- src/lib/components/ContactSection.svelte -->

<script lang="ts">
  import { Button, SectionHeadline } from "$components";

  let contactName = $state("");
  let contactEmail = $state("");
  let contactMessage = $state("");

  let isFormInvalid = $state(false);  // add a new state to tracking if user inputs are valid

  function onSubmit(event: Event) {
    event.preventDefault();

    if (!contactName || !contactEmail || !contactMessage) {
    // ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ this is validation rule which means all fields are required
    //                                                    of course we can implement more complex validation rules

      isFormInvalid = true; // when the above condition result is true, which means the form is invalid
                            // then we set isFormInvalid to true to let the component know that the form is invalid now
                                  
      return
    }

    // Send the form data to the server
  }
</script>

<section class="mt-l">
  <SectionHeadline sectionName="contact-form">Let's talk</SectionHeadline>
  <div class="form-container default-margin mt-m">
    <form onsubmit={onSubmit}>
      <input
        class="text-input mb-m"
        class:input-error={isFormInvalid && !Boolean(contactName.length)}
  <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ not only isFormInvalid, but also check if the input field is empty or not individually -->
       
        placeholder="Your Name"
        bind:value={contactName}
      />
      <input
        class="text-input mb-m"
        class:input-error={isFormInvalid && !Boolean(contactEmail.length)}
   <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ -->
        placeholder="Your Email"
        bind:value={contactEmail}
      />
      <textarea
        class:input-error={isFormInvalid && !Boolean(contactMessage.length)}
   <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ -->
        placeholder="Tell we what's up."
        bind:value={contactMessage}></textarea>
      <Button>Submit</Button>
    </form>

    ...omit
```

Then, we want the error styling to be removed when the user starts typing in any input field. We can achieve this by adding an effect that listens to any change in the input fields and resets the `isFormInvalid` state to `false`:

```svelte
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

    // Send the form data to the server
  }

  // whenever any input field is changed, reset isFormInvalid to false
  $effect(() => { if (contactName || !contactName) isFormInvalid = false; })
              //  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ in Svelte, $effect is not like useEffect in React, there is no dependencies array to set
              //                                   So we need to add dependent state in the callback function to make it work
                
  $effect(() => { if (contactEmail || !contactEmail) isFormInvalid = false; })
                  //  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ (contactName || !contactName || contactEmail || !contactEmail) cannot work when contactEmail is changed
                  //                                so we need to write multiple $effect to listen to each state individually
                    
  $effect(() => { if (contactMessage || !contactMessage) isFormInvalid = false; })
                  // ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

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

    ...omit
```

Next, we can start to [[2024-12-06_Create-api-endpoint-for-sending-email:-make-sure-inputs-data-to-backend|create api endpoint for sending email: make sure inputs data to backend]].
