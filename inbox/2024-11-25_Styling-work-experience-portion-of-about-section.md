---
date: 2024-11-25
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Styling work experience portion of about section

In [[2024-11-25_Create-structure-of-work-experience-(2nd-portion-of-about-section)|this topic]] we created the structure of the work experience portion of the about section. Now we will style it:

```svelte
<!-- src/lib/components/ExperienceTable.svelte -->

<script lang="ts">
  interface WorkExperience {
    title: string;
    company: string;
    startDate: string;
    endDate?: string;
  }

  const workExperience: WorkExperience[] = [
    {
      title: "Software Engineer",
      company: "Company A",
      startDate: "2019-01",
    },
    {
      title: "Software Engineer",
      company: "Company B",
      startDate: "2018-01",
      endDate: "2019-01",
    },
    {
      title: "Software Engineer",
      company: "Company C",
      startDate: "2017-01",
      endDate: "2018-01",
    },
    {
      title: "Software Engineer",
      company: "Company D",
      startDate: "2016-01",
      endDate: "2017-01",
    },
  ];
</script>

<div class="work-experience default-margin mt-l">
                       <!-- ^^^^^^^^^^^^^^^^^^^ -->
  <ul class="work-experience-list">
    {#each workExperience as job}
      <li class="work-experience-item">
        <article>
          <h3 class="semi-bold mb-xs">{job.title}</h3>
        <!-- ^^^^^^^^^^^^^^^^^^^^^^^^ not only margin space, we use utility classes to style the font weight -->
          <div class="company-and-date">
            <p>{job.company}</p>
            <p class="dark-grey">
          <!-- ^^^^^^^^^^^^^^^^ not only color, we use utility classes to style the font color -->
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

<!-- add styles to classes below: -->
<style>
  .work-experience {
    display: flex;
    justify-content: space-between;
    width: 100%;
  }

  .work-experience-list {
    width: 50%;
  }

  .headline {
    text-align: right;
  }

  .work-experience-item {
    margin-bottom: 1px solid #f0eded;
    padding-bottom: 12px;
  }

  .work-experience-item:not(:first-of-type) {
  /*                   ^^^^^^^^^^^^^^^^^^^ we wish first work-experience-item to have no top padding */
    padding-top: 16px;
  }

  .work-experience-item p {
    margin-bottom: 0;
  }

  .company-and-date {
    display: flex;
    justify-content: space-between;
  }
</style>

```

We can use utility class to simplify styling and make it more consistent across the project. If you are very like the way to use utility classes, you can try to use TailwindCSS.
