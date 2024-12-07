---
date: 2024-12-07
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Footer of the landing page

We want to create a simple footer with reverse color scheme to the page. The footer will contain the basic information about the author, links to social media, and a copy right notice. 

```svelte
<!-- src/lib/components/Footer.svelte  -->

<footer class="footer">
  <div class="footer-container">
    <h3 class="semi-bold mb-xs">Matt Chang</h3>
    <div class="mb-l">
      <a
        href="https://github.com/echoedinvoker"
        target="_blank"
        class="footer-link"
        aria-label="GitHub"  <--- aria-label is used to provide a label for screen readers, which is required for accessibility
      >GitHub</a>
      <a
        href="https://www.linkedin.com/in/matt-chang-7a3b9b1a4/"
        target="_blank"
        class="footer-link"
        aria-label="LinkedIn"
      >LinkedIn</a>
      <a
        href="mailto:echoedinvoker@gmail.com"
        class="footer-link"
        aria-label="Email"
      >Email</a>
    </div>
    <p>
      &copy; 2021 Matt Chang. All rights reserved.
 <!-- ^^^^^^ using HTML entity to display some special characters or symbols is a good practice -->
    </p>
  </div>
</footer>

<style>
  .footer {
    /* reverse color scheme */
    background-color: black;
    color: white;

    padding: 40px 20px;

    /* here we don't need to use flexbox, but `text-align: center` because we only have one column */
    text-align: center;
  }
  .footer-link {
    margin: 0 10px; /* without flexbox, we still can use margin to separate the links */
  }
</style>

```

```svelte
~/D/g/s/s/dev-portfolio > cat src/lib/components/index.ts
export { default as HeroSection } from './HeroSection.svelte';
export { default as Button } from './Button.svelte';
export { default as SectionHeadline } from './SectionHeadline.svelte';
export { default as AboutSection } from './AboutSection.svelte';
export { default as ExperienceTable } from './ExperienceTable.svelte';
export { default as MyWorkSection } from './MyWorkSection.svelte';
export { default as SkillsSection } from './SkillsSection.svelte';
export { default as ContactSection } from './ContactSection.svelte';
export { default as Header } from './Header.svelte';
export { default as Footer } from './Footer.svelte';
```

```svelte
~/D/g/s/s/dev-portfolio > cat src/routes/+layout.svelte
<script lang="ts">
  import { Header } from '$components';
    import Footer from '$components/Footer.svelte';
   
  import '../app.css';

  let { children } = $props();
</script>

<Header />
{@render children()}
<Footer />

```
