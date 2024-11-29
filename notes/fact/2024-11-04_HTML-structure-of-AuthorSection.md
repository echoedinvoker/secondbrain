---
date: 2024-11-04
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# HTML structure of AuthorSection

[[2024-11-04_HTML-structure-is-first-step-of-building-page|HTML structure is first step of building page]], this is a simple HTML structure of AuthorSection component, it is last section of the landing page.

Here we don't consider any styling, just focus on the structure (contents, semantics and accessibility).

```svelte
<!-- src/lib/components/AuthorSection.svelte  -->
<script lang="ts">
  import authorPic from '$assets/profile_pic.jpg';
</script>

<section class="author-section">
  <h2>About the Author</h2>
  <div class="author-container">
    <div class="author-text">
      <p>
        Matthew is a software engineer and writer from the UK. He has been 
        industry for over 10 years and has a passion for writing about tech
        development. Matthew is a self-taught developer and has worked with
        languages and frameworks.
      </p>
      <p>
        He is currently working as a freelance software developer and techn
        time, Matthew enjoys reading, writing, and playing video games. He 
        science fiction and fantasy literature. Matthew is always looking f
        learn and grow as a developer and writer.
      </p>
    </div>
    <img src={authorPic} alt="Author Matthew Chang" />
  </div>
</section>
```

```ts
// src/lib/components/index.ts 
export { default as HeroSection } from './HeroSection.svelte';
export { default as Button } from './Button.svelte';
export { default as ChapterPreview } from './ChapterPreview.svelte';
export { default as AuthorSection } from './AuthorSection.svelte';

```

```svelte
<!-- src/routes/+page.svelte -->
<script>
  import { HeroSection, ChapterPreview, AuthorSection } from '$components';
</script>

<HeroSection />
<ChapterPreview />
<AuthorSection />

```

