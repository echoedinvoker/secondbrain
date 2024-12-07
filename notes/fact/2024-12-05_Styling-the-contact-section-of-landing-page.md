---
date: 2024-12-05
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Styling the contact section of landing page

After [[2024-12-05_Create-structure-of-contact-section|creating structure of contact section]], we start to style it:

```svelte
<script lang="ts">
  import { Button, SectionHeadline } from "$components";

  function onSubmit(event: Event) {
    event.preventDefault();
    console.log(event);
  }
</script>

<section class="mt-l">
          <!-- ^^^^ -->
  <SectionHeadline sectionName="contact-form">Let's talk</SectionHeadline>
  <div class="form-container default-margin mt-m">
                        <!-- ^^^^^^^^^^^^^^^^^^^ -->
    <form onsubmit={onSubmit}>
      <input class="text-input mb-m" placeholder="Your Name" />
                          <!-- ^^^^ -->
      <input class="text-input input-error mb-m" placeholder="Your Email" />
                          <!-- ^^^^^^^^^^^^^^^^ input-error is for error input situation -->
                          <!--                  it should be toggled dynamically -->
      <textarea placeholder="Tell we what's up."></textarea>
                                           <!-- ^^ make sure to close the tag properly, or the placeholder won't work -->
      <Button>Submit</Button>
    </form>
    <div class="form-text">
      <h3 class="bold mb-s">Talk to me about your project</h3>
     <!-- ^^^^^^^^^^^^^^^^^ -->
      <p>
        I'm currently available for freelance work. If you have a project that you want to get started, think you need my help with something or just fancy saying hey, then get in touch. I'll do my best to get back to you as soon as possible.
      </p>
    </div>
  </div>
</section>

<!-- add specific styles below: -->
<style>
  section {
    padding-bottom: 140px; /* because this is the last section of the page */
                          /* add some padding to make sure the last content is visible */
  }
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
    align-items: flex-start; /* align input fields to left */
    width: 45%;  /* with .form-container and .form-text, we know tht space left is 16% in the middle */
  }
  form * { /* we should style the text style in the form separately */
    font-size: 20px;
    font-family: "Inter Tight", sans-serif; /* same as we used in the whole project */
                                            /* but we need to style it separately */
    font-weight: 500;
    color: black
  }
  textarea, input {
    width: 100%;
    background-color: rgba(0, 0, 0, 0.035);
    border-radius: 8px;
    padding: 4px 12px;
    /* not only border, outline should be removed too */
    outline: none;
    border: none;
  }
  input {
    height: 40px;
  }
  textarea {
    height: 120px;
    margin-bottom: 40px;
  }
  textarea::placeholder, input::placeholder {
    font-weight: 400;   /* override the font-weight of `form *` */
                        /* slightly lighter */
  }

  /* below styles are for error input situation */
  .input-error {
    background-color: rgba(223, 87, 87, 0.667);
  }

  .input-error::placeholder {
    color: white;
  }



</style>

```

