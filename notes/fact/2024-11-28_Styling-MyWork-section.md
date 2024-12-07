---
date: 2024-11-28
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Styling MyWork section

After [[2024-11-27_Pass-Sanity-data-to-MyWork-section-of-landing-page-and-build-its-structure|building structure of MyWork section]], we can now style it:

```svelte
<!-- src/lib/components/MyWorkSection.svelte -->

<script lang="ts">
  import { SectionHeadline } from "$components";

  interface MyWorkSectionProps {
    projects: ProcessedProject[];
  }

  let { projects }: MyWorkSectionProps = $props();
  const mainProject = projects[0];
  const otherProjects = projects.slice(1);
</script>

<section class="my-work-section">
  <SectionHeadline sectionName="my-work">My Work</SectionHeadline>
  <div class="projects-container default-margin mt-m">
                            <!-- ^^^^^^^^^^^^^^^^^^^ -->
    <article class="main-project">
      <a href={`/work/${mainProject.slug}`}>
        <img src={mainProject.projectImageUrl} alt={mainProject.name} />
      </a>
      <div class="project-info mt-s">
                          <!-- ^^^^ -->
        <div class="title-and-company">
          <h3>{mainProject.name}</h3>
          <p class="company">{mainProject.company}</p>
        </div>
        <a href={`/work/${mainProject.slug}`} class="btn-to-article">&rarr;</a>
      </div>
    </article>
    <div class="more-projects-container mt-m">
                                   <!-- ^^^^ -->
      {#each otherProjects as project}
        <article class="project">
          <a href={`/work/${project.slug}`}>
            <img src={project.projectImageUrl} alt={project.name} />
          </a>
          <div class="project-info mt-s">
                              <!-- ^^^^ -->
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

<!-- add styles below: -->
<style>
  /* regular styles for all img elements go here */
  img {
    width: 100%;
    border-radius: 20px;
    object-fit: cover;
    cursor: pointer;
  }

  /* img under different parent has different height */
  .more-projects-container img {
    height: 350px;
  }
  .main-project img {
    max-height: 450px;
  }

  .more-projects-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
  }

  .project {
    width: 48%;  /* so in .more-projects-container we have 2 columns and there is 4% space between them */
  }

  .project-info {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .btn-to-article {
    display: block;
    font-size: 40px;
    border: 1px solid black;
    border-radius: 40px;
    padding: 0px 24px;
  }

  .company {
    font-size: 18px;
    margin-bottom: 0; /* this is important to remove the default margin-bottom of the p tag */
                      /* otherwise the height of .project-info will be bigger than .btn-to-article */
                      /* and it will make button to be not vertically centered */
  }
</style>

```

Regarding the margin-bottom issue of the p tag above, we can also directly remove it in the global CSS at the beginning, so we don't need to handle it in every component.

