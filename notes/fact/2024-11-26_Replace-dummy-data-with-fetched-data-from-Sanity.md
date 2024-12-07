---
date: 2024-11-26
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Replace dummy data with fetched data from Sanity

After [[2024-11-26_Using-Sanity-Client-to-fetch-data-from-Sanity-Studio-to-SvelteKit-project|we make sure fetched data from Sanity]] is correct, we can start to replace the dummy data with it.

```svelte
<!-- src/routes/+page.svelte -->

<script lang="ts">
  import { HeroSection, AboutSection } from "$components";

  let { data } = $props();
  let { workExperience } = data;
//      ^^^^^^^^^^^^^^^^^^^^^^^ destructure the workExperience array from the data object
//                              but it will break the reactivity of the data object
//                              because we don't use $derived, if we ensure that the data object won't change
//                              making it static is totally fine 
</script>

<HeroSection />
<AboutSection {workExperience} />
         <!-- ^^^^^^^^^^^^^^^^ props to AboutSection component -->


<!-- src/lib/components/AboutSection.svelte  -->

<script lang="ts">
  import { Button, SectionHeadline, ExperienceTable } from "$components";
  import image from "$assets/about-me.jpeg";
  import { goto } from "$app/navigation";
  import type { DevExperience } from "$lib/types/sanity";

  // define the props interface to make this comoponent accept workExperience form parent
  interface AboutSectionProps {
    workExperience: DevExperience[];
  }

  // get the workExperience prop
  let { workExperience }: AboutSectionProps = $props();

  function onclick() {
    goto("/#contact-form");
  }
</script>

<section class="about-me mb-l">
  <SectionHeadline sectionName={"about-me"}>About Me</SectionHeadline>
  <div class="content-container mt-m default-margin">
    <!-- ...omit -->
  </div>

  <ExperienceTable {workExperience} />
              <!-- ^^^^^^^^^^^^^^^^ keep prop workExperience to next component -->
</section>



<!-- src/lib/components/ExperienceTable.svelte  -->

<script lang="ts">
  import type { DevExperience } from "$lib/types/sanity";

  // same as AboutSectionProps, define the props interface
  interface ExperienceTableProps {
    workExperience: DevExperience[];
  }

  // same as AboutSection, get the workExperience prop
  let { workExperience }: ExperienceTableProps = $props();


  // remove the dummy data and its interface, it will be replaced by the fetched data from Sanity above

</script>

<div class="work-experience default-margin mt-l">
  <ul class="work-experience-list">
    {#each workExperience as job}
      <li class="work-experience-item">
        <article>
          <h3 class="semi-bold mb-xs">{job.jobTitle}</h3>
          <div class="company-and-date">
            <p>{job.company}</p>
            <p class="dark-grey">
              {job.startDate}
              {#if job.endDate}
                / {job.endDate}
              {:else}
                / present
              {/if}
            </p>
          </div>
        </article>
      </li>
    {/each}
  </ul>
  <h2 class="headline">Past work <br /> Experience</h2>
</div>

```


There are several key points to note in the above process:

1. destructure props without `$derived` will break the reactivity of the data object, but it's totally fine if we ensure that the data object won't change

2. The process of drilling props in the workExperience props above is only slightly, we can choose to use `context` to remove the drilling, but in this example, it doesn't seem complicated enough to require using it. We will use `context` in the third project.
