---
date: 2024-11-25
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create reusable section headline with id and underscore decoration

```bash
~/D/g/s/s/dev-portfolio > tree src/
src/
├── app.css
├── app.d.ts
├── app.html
├── lib
│   ├── components
│   │   ├── AboutSection.svelte  # new component for About section, using SectionHeadline
│   │   ├── Button.svelte
│   │   ├── HeroSection.svelte
│   │   ├── index.ts # add export for SectionHeadline and AboutSection
│   │   │   # export { default as SectionHeadline } from './SectionHeadline.svelte';
│   │   │   # export { default as AboutSection } from './AboutSection.svelte';
│   │   │
│   │   └── SectionHeadline.svelte  # new component for reusable section headline
│   └── index.ts
└── routes
    ├── +layout.svelte
    └── +page.svelte

```

```svelte
<!-- src/lib/components/SectionHeadline.svelte  -->

<script lang="ts">
  import type { Snippet } from "svelte";

  interface SectionHeadlineProps {
    children: Snippet;
    sectionName: string;  // id for navigation on the same page
  }
  let { children, sectionName }: SectionHeadlineProps = $props();
</script>

<div class="default-margin" id={sectionName}>
       <!-- ^^^^^^^^^^^^^^  ^^^^^^^^^^^^^^^^ for navigation on the same page -->
       <!-- in fact, I have no idea why lecturer put `default-margin` class here -->
        
  <h2>{@render children()}</h2>
  <div class="underscore"></div> <!-- underline decoration -->
</div>


<!-- src/lib/components/AboutSection.svelte  -->

<script lang="ts">
  import { SectionHeadline } from "$components";
</script>

<section class="about-section">  <!-- I thought class `default-margin` should be here -->

  <!-- here we use AboutSection component as a example, We will reuse SectionHeadline at the start of each section (excluding HeroSection) -->
  <SectionHeadline sectionName="about-me">About Me</SectionHeadline> 

  <!-- other content... -->
</section>


<!-- src/routes/+page.svelte -->

<script lang="ts">
  import { HeroSection, AboutSection } from "$components";
  //                    ^^^^^^^^^^^^
</script>

<HeroSection />
<AboutSection />  <!-- add new section to page -->

```

