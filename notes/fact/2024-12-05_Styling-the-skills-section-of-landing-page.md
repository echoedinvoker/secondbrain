---
date: 2024-12-05
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Styling the skills section of landing page

In [[2024-12-04_Build-structure-of-skills-section-of-landing-page|this topic]], we finished the structure of the skills section, now we can start to style it:

```svelte
<script lang="ts">
    import { SectionHeadline } from "$components";

  interface SkillsSectionProps {
    skills: Skill[];
  }

  let { skills }: SkillsSectionProps = $props();
</script>

<section class="skills-section">
  <SectionHeadline sectionName="skills">Skills</SectionHeadline>
  <div class="wrapper default-margin">
                 <!-- ^^^^^^^^^^^^^^ -->
    <div class="skills-container mt-m">
                            <!-- ^^^^ -->
      {#each skills as skill}
        <i class={skill.iconClass}></i>
      {/each}
    </div>
  </div>
</section>

<!-- add specific styles below: -->
<style>
  .wrapper {  /* here we use flex to center its content (skill icons) */
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .skills-container { /* let skill icons wrap in a container of 40% width */
    width: 40%;
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    row-gap: 16px;
    column-gap: 10px;
  }
  i { /* set icon size and color */
    font-size: 80px;
    color: black;
  }
</style>

```

