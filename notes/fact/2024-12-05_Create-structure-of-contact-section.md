---
date: 2024-12-05
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create structure of contact section

Create new file `ContactSection.svelte` and write the structure of the contact section in it:

```svelte
<!-- src/lib/components/ContactSection.svelte  -->

<script lang="ts">
  import { Button, SectionHeadline } from "$components";

  function onSubmit(event: Event) {
    event.preventDefault();
    console.log(event);
  }
</script>

<section>
  <SectionHeadline sectionName="contact-form">Let's talk</SectionHeadline>
                           <!-- ^^^^^^^^^^^^ our HeroSection component has click handler with `goto` function -->
                           <!--              we should pass `#contact-form` to it to scroll to this section -->
  <div class="form-container">
    <form onsubmit={onSubmit}>
      <input class="text-input" placeholder="Your Name" />
      <input class="text-input" placeholder="Your Email" />
      <textarea placeholder="Tell we what's up."></textarea>
      <Button>Submit</Button>
    </form>
    <div class="form-text">
      <h3>Talk to me about your project</h3>
      <p>
        I'm currently available for freelance work. If you have a project that you want to get started, think you need my help with something or just fancy saying hey, then get in touch. I'll do my best to get back to you as soon as possible.
      </p>
    </div>
  </div>
</section>
```

```ts
// src/lib/components/index.ts 

export { default as HeroSection } from './HeroSection.svelte';
export { default as Button } from './Button.svelte';
export { default as SectionHeadline } from './SectionHeadline.svelte';
export { default as AboutSection } from './AboutSection.svelte';
export { default as ExperienceTable } from './ExperienceTable.svelte';
export { default as MyWorkSection } from './MyWorkSection.svelte';
export { default as SkillsSection } from './SkillsSection.svelte';
export { default as ContactSection } from './ContactSection.svelte';  // Add to index.ts for better imports

```

Import to the `+page.svelte` file on the root route:

```svelte
<!-- src/routes/+page.svelte  -->

<script lang="ts">
  import { HeroSection, AboutSection, MyWorkSection, SkillsSection, ContactSection } from "$components";
  //                                                                ^^^^^^^^^^^^^^

  let { data } = $props();
  let { workExperience, projects, skills } = data;
</script>

<HeroSection />
<AboutSection {workExperience} />
<MyWorkSection {projects} />
<SkillsSection {skills} />

<ContactSection />  <!-- add this line, this is the last section of the landing page -->
                    <!-- and it's the only one section that accept data from the user -->

```
