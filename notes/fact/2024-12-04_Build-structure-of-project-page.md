---
date: 2024-12-04
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Build structure of project page

We can refer to the type definitions of processed project data to build the structure of the project page.

```ts
// src/lib/types/sanity.d.ts

//...

interface ProcessedProject {
  name: string;
  company: string;
  slug: string;
  projectImageUrl: string;
  dateAccomplished: string;
  stack: string[];
  content: Array<ProcessedTextContent | ProcessedImageContent>;
}

interface ProcessedTextContent {
  type: 'text';  // we added this property for type checking
  style: 'normal' | 'h1' | 'h2' | 'h3' | 'h4' | 'h5' | 'h6' | 'blockquote';
  //     ^^^^^^^^ this is tag `p`
  textToRender: string;
}

interface ProcessedImageContent {
  type: 'image';
  url: string;
}

```

```svelte
<!-- src/routes/work/\[slug\]/+page.svelte -->

<script lang="ts">
  let { data } = $props();

  // based on the ProcessedProject type, we can destructure properties that we need to render
  const { name, company, projectImageUrl, dateAccomplished, stack, content } = data.project;

  // in type ProcessedTextContent, we have a property `style` that we want to use as tag
  // but only value `normal` should be converted to `p`, so we need a function to get tag from style
  function getTagFromStyle(style: ProcessedTextContent['style']): string {
    if (style === 'normal') return 'p';
    return style;
  }
</script>

<main class="work-page">
  <h4>{company}</h4>
  <div class="underscore"></div>
  <h2>{name}</h2>
  <img class="project-image" src={projectImageUrl} alt="" />  <!-- because this is only for decoration, we can leave alt empty string -->
  <div class="project-container">
    <div class="meta-data">
      <h3>Date</h3>
      <p>{dateAccomplished}</p>
      <h3>Tech Stack</h3>
      <ul>
        {#each stack as skill}
          <li>{skill}</li>
        {/each}
      </ul>
    </div>
    <div class="project-content">
      {#each content as block}
        {#if block.type === 'text'}
        <!-- ^^^^^^^^^^^^^^^^^^^^^ as we said in type definition, we have a property `type` to differentiate between text and image -->
          <svelte:element this={getTagFromStyle(block.style)}>{block.textToRender}</svelte:element>
      <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ svelte:element is used to dynamically render elements with different tags -->
      <!--                                                    tag will be specified by the value of `this` attribute -->
        {:else}
          <img class="content-image" src={block.url} alt="" />
        {/if}
      {/each}
         
    </div>
  </div>
</main>
```
