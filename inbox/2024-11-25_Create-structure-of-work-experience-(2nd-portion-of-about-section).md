---
date: 2024-11-25
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create structure of work experience (2nd portion of about section)

```bash
~/D/g/s/s/dev-portfolio > tree src/

src/
├── app.css
├── app.d.ts
├── app.html
├── assets
│   └── about-me.jpeg
├── lib
│   ├── components
│   │   ├── AboutSection.svelte
│   │   ├── Button.svelte
│   │   ├── ExperienceTable.svelte  # added, write content below
│   │   ├── HeroSection.svelte
│   │   ├── index.ts # export { default as ExperienceTable } from "./ExperienceTable.svelte";
│   │   └── SectionHeadline.svelte
│   └── index.ts
└── routes
    ├── +layout.svelte
    └── +page.svelte

```
```svelte
<!-- src/lib/components/ExperienceTable.svelte  -->

<script lang="ts">
  interface WorkExperience {
    title: string;
    company: string;
    startDate: string;
    endDate?: string;
  }

  // in write structure step, we use dummy data to create the structure
  // data will come from CMS sanity in the future
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

<div class="work-experience">
  <ul class="work-experience-list">
    {#each workExperience as job}
      <li class="work-experience-item">

        <!-- we don't directly use h3 or div under li, instead we use article for better semantics -->
        <article>
          <h3>{job.title}</h3>
          <div class="company-and-date">
            <p>{job.company}</p>
            <p>
              {job.startDate}
              {#if job.endDate}  <!-- endDate in optional, so we use #if block here -->
                / {job.endDate}  <!-- we see that #if block can be used in the portion of tag content -->
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
                            <!-- ^^^^^^ so we can only use one h2 tag here -->
                            <!--        if we use multiple h2 tags, we need to wrap them in a div for class `headline` -->
                            <!--        which is more tedious -->
</div>
```

Because `ExperienceTable` is portion of action section, we need to import it in `AboutSection` component as below:

```svelte
<!-- src/lib/components/AboutSection.svelte -->

<script lang="ts">
  import { Button, SectionHeadline, ExperienceTable } from "$components";
  //                                ^^^^^^^^^^^^^^^
  import image from "$assets/about-me.jpeg";
  import { goto } from "$app/navigation";

  function onclick() {
    goto("/#contact-form");
  }
</script>

<section class="about-me mb-l">
  <SectionHeadline sectionName={"about-me"}>About Me</SectionHeadline>
  <div class="content-container mt-m default-margin">
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

  <ExperienceTable />  <!-- added -->

</section>

<style>
  .content-container {
    display: flex;
    justify-content: space-between;
    align-items: stretch;
  }

  .image {
    width: 42%;
    border-radius: 20px;
    object-fit: cover;
  }

  .text {
    width: 55%;
    text-align: left;
  }
</style>

```

In the above, we used dummy data. In the future, we plan to use a headless CMS to manage data and be able to import this data into this component. [[2024-11-25_Create-sanity-project-locally|This topic]] has started teaching us how to set up a sanity project locally.
