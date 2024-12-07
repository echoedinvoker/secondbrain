---
date: 2024-12-04
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Build structure of skills section of landing page

In [[2024-12-04_Extract-type-from-skills-schema-and-fetch-skills-data-on-the-frontend|this topic]], we make sure that the frontend page can get correct skills data, now we can start to build the structure of skills section:

1. create new file `SkillsSection.svelte` which accept `skills` prop and render the skills section:

```svelte
<!-- dev-portfolio/src/lib/components/SkillsSection.svelte  -->

<script lang="ts">
    import { SectionHeadline } from "$components";

  interface SkillsSectionProps {
    skills: Skill[];
  }

  let { skills }: SkillsSectionProps = $props();
</script>

<section class="skills-section">
  <SectionHeadline sectionName="skills">Skills</SectionHeadline>
  <div class="wrapper">
    <div class="skills-container">
      {#each skills as skill}
        <i class={skill.iconClass}></i>
    <!-- ^ this tag means `icon`, it must be paired with a devicon icon class for rendering icon -->
    <!--   you should already install devicon CSS in html head, or the icon class not work -->
      {/each}
    </div>
  </div>
</section>
```

2. use it in the root page component:

```svelte
<!-- dev-portfolio/src/routes/+page.svelte  -->

<script lang="ts">
  import { HeroSection, AboutSection } from "$components";
  import MyWorkSection from "$components/MyWorkSection.svelte";
  import SkillsSection from "$components/SkillsSection.svelte";  // import it

  let { data } = $props();
  let { workExperience, projects, skills } = data;
  //                              ^^^^^^
</script>

<HeroSection />
<AboutSection {workExperience} />
<MyWorkSection {projects} />
<SkillsSection {skills} />  <!-- use it and props `skills` to it -->

```

Then, we can start to [[2024-12-05_Styling-the-skills-section-of-landing-page|style the skills section]]
