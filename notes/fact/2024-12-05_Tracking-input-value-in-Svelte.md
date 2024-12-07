---
date: 2024-12-05
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Tracking input value in Svelte

In [[2024-12-05_Styling-the-contact-section-of-landing-page|this case]], if we want to track the input values of the contact form, we can simply use `bind:` directive to bind the attribute `value` of the input to a `state`:

```svelte
<!-- src/lib/components/ContactSection.svelte  -->

<script lang="ts">
  import { Button, SectionHeadline } from "$components";

  // create states to track the input/textarea values
  let contactName = $state("");
  let contactEmail = $state("");
  let contactMessage = $state("");

  $inspect({ contactName, contactEmail, contactMessage });  // inspect the states, if binding is working,
                                                            // we can see the values in the console whenever we type in

  function onSubmit(event: Event) {
    event.preventDefault();

    console.log(contactName, contactEmail, contactMessage) // with tracking states, theoretically, we can do something with the values
                                                           // such as submit user inputs to the server...
  }
</script>

<section class="mt-l">
  <SectionHeadline sectionName="contact-form">Let's talk</SectionHeadline>
  <div class="form-container default-margin mt-m">
    <form onsubmit={onSubmit}>
      <input class="text-input mb-m" placeholder="Your Name" bind:value={contactName} />
                                                        <!-- ^^^^^^^^^^^^^^^^^^^^^^^^ bind `value` to state `contactName` -->
      <input class="text-input mb-m" placeholder="Your Email" bind:value={contactEmail} />
                                                         <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^ bind `value` to state `contactEmail` -->
      <textarea placeholder="Tell we what's up." bind:value={contactMessage}></textarea>
                                            <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^ bind `value` to state `contactMessage` -->
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
