---
date: 2024-11-25
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create structure of about section

After [[2024-11-25_Create-reusable-section-headline-with-id-and-underscore-decoration|this topic]], we keep working on the about section. We need to create a structure for the about section:

```svelte
<!-- src/lib/components/AboutSection.svelte  -->

<script lang="ts">
  import { Button, SectionHeadline } from "$components";
  import image from "$assets/about-me.jpeg"; // make a alias $assets to src/assets in svelte.config.js
                                             // and download the image file `about-me.jpeg` to it
  import { goto } from "$app/navigation";

  function onclick() {
    goto("/#contact-form");
  }
</script>

<section class="about-me">
  <SectionHeadline sectionName={"about-me"}>About Me</SectionHeadline>

  <!-- add content portion below -->
  <div class="content-container">
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
    </div>
  </div>

  <!-- There is experience portion, but its data comes from the headless CMS, so we will write it later. -->
  <!-- <ExperienceTable {workExperience} /> -->
</section>

```

