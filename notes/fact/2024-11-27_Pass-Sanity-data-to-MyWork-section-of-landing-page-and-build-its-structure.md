---
date: 2024-11-27
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Pass Sanity data to MyWork section of landing page and build its structure

In [[2024-11-27_Implement-function-to-convert-raw-Sanity-data-to-usable-data-for-frontend|this topic]], we already finished the function to convert raw projects data from Sanity to usable data for the frontend. Now, we need to pass this converted data to the MyWork section of the landing page and build its structure.

This data in **NOT** sensitive, so we can fetch it in `page.ts` and props it down to the `MyWorkSection` component.

```ts
// src/routes/+page.ts 

import type { PageLoad } from './$types';
import sanityClient, { processProjectEntries } from '$lib/utils/sanity'

export const load: PageLoad = async () => {
  const workExperience: DevExperience[] = await sanityClient.fetch(
    '*[_type == "devExperience"] | order(starDate desc)'
  )

  // fetch projects data from Sanity
  const rawProjects: SanityProject[] = await sanityClient.fetch(
    '*[_type == "project"] | order(dateAccomplished desc)'
    //                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ order is matter in our render,
    //                                                    we let newest project comes first
  )

  // process projects data is too nested, so we use a helper function to parse it to usable data
  const projects = rawProjects.map(processProjectEntries);
  //                               ^^^^^^^^^^^^^^^^^^^^^ fn to parse, we have implemented it in previous topic

  return {
    workExperience,
    projects  // add projects to the return object, then it will be accessible in the `+page.svelte` prop `data`
  }
};
```
j
```svelte
~/D/g/s/s/dev-portfolio > cat src/routes/+page.svelte
<script lang="ts">
  import { HeroSection, AboutSection } from "$components";
  import MyWorkSection from "$components/MyWorkSection.svelte";

  let { data } = $props();
  let { workExperience, projects } = data;
  //                    ^^^^^^^^ 
</script>

<HeroSection />
<AboutSection {workExperience} />

<MyWorkSection {projects} />  <!-- pass projects data to MyWorkSection component -->
```

Create `MyWorkSection` component and build its structure:

```svelte
<!-- src/lib/components/MyWorkSection.svelte  -->

<script lang="ts">
  import { SectionHeadline } from "$components";

  interface MyWorkSectionProps {
    projects: ProcessedProject[];
  }

  let { projects }: MyWorkSectionProps = $props();

  // main project and other projects are different in layout, so we separate them
  const mainProject = projects[0];  // get first project as main project
  const otherProjects = projects.slice(1);  // get the rest as other projects
</script>

<section class="my-work-section">
  <SectionHeadline sectionName="my-work">My Work</SectionHeadline>
  <div class="projects-container">
    <article class="main-project">
      <a href={`/work/${mainProject.slug}`}>
        <img src={mainProject.projectImageUrl} alt={mainProject.name} />
      </a>
      <div class="project-info">
        <div class="title-and-company">
          <h3>{mainProject.name}</h3>
          <p class="company">{mainProject.company}</p>
        </div>
        <a href={`/work/${mainProject.slug}`} class="btn-to-article">&rarr;</a>
      </div>
    </article>
    <div class="more-projects-container">
      {#each otherProjects as project}  <!-- number of other projects are dynamic, so we use #each to render them -->

        <!-- basically, the structure is the same with main project, just replace `mainProject` with `project` -->
        <article class="project">
          <a href={`/work/${project.slug}`}>
            <img src={project.projectImageUrl} alt={project.name} />
          </a>
          <div class="project-info">
            <div class="title-and-company">
              <h3>{project.name}</h3>
              <p class="company">{project.company}</p>
            </div>
            <a href={`/work/${project.slug}`} class="btn-to-article">&rarr;</a>
          </div>
        </article>

      {/each}
    </div>
  </div>
</section>
```

Next, we can start to [[2024-11-28_Styling-MyWork-section|style MyWork section]]
