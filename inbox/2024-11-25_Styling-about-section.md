---
date: 2024-11-25
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Styling about section

In [[2024-11-25_Create-structure-of-about-section|this topic]], we create the structure of about section (excluding experience portion), now we will style it:

```svelte
<!-- src/lib/components/AboutSection.svelte -->

<script lang="ts">
  import { Button, SectionHeadline } from "$components";
  import image from "$assets/about-me.jpeg";
  import { goto } from "$app/navigation";

  function onclick() {
    goto("/#contact-form");
  }
</script>

<section class="about-me mb-l">
                    <!-- ^^^^ -->
  <SectionHeadline sectionName={"about-me"}>About Me</SectionHeadline>
  <div class="content-container mt-m default-margin">
                           <!-- ^^^^^^^^^^^^^^^^^^^ because we set `default-margin` inside `SectionHeadline` component -->
                           <!--                     so we need to add `default-margin` class to each elements under the section -->
    <img class="image" src={image} alt="" />
    <div class="text">
      <p>
        Hey, I'm Niklas, the driving force behind my freelancing agency
        specializing in web development and data science.
      </p>
      <p>
        With years of hands-on experience tackling real-world coding challenges
        and meeting diverse client needs, I've built a deep understanding of the
        tech landscape. But beyond coding, my true passion lies in teaching.
        I've had the privilege of guiding hundreds of aspiring developers,
        focusing on making coding both practical and accessible. My goal is to
        translate complex concepts into actionable skills that you can apply
        right away.
      </p>
      <p>
        Through my courses, I’m committed to sharing the insights and lessons
        I’ve learned in the field, so you can skip the pitfalls and fast-track
        your development journey. If you're eager to learn from real-world
        experience and practical know-how, let's dive in together.
      </p>
      <Button className="mt-m" {onclick}>Tell me about your project</Button>
         <!-- ^^^^^^^^^^^^^^^^ -->
    </div>
  </div>
</section>

<style>

  /* make content container flexbox to align image and text side by side */
  .content-container {
    display: flex;
    justify-content: space-between;
    align-items: stretch;
  }

  .image {
    width: 42%;
    border-radius: 20px;
    object-fit: cover; /* to make image ratio fixed */
  }

  .text {
    width: 55%;  /* combined with image width, it left 3% space as gap */
    text-align: left;
  }
</style>

```
