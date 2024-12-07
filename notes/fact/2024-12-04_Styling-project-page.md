---
date: 2024-12-04
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Styling project page

In [[2024-12-04_Build-structure-of-project-page|this topic]], we build the structure of the project page. Now, we can style it:

```svelte
<script lang="ts">
  let { data } = $props();
  const { name, company, projectImageUrl, dateAccomplished, stack, content } = data.project;

  function getTagFromStyle(style: ProcessedTextContent['style']): string {
    if (style === 'normal') return 'p';
    return style;
  }
</script>

<!-- use pre-built utility classes to do common styling tasks, it's easy and fast -->

<main class="work-page default-margin">
                  <!-- ^^^^^^^^^^^^^^ -->
  <h4>{company}</h4>
  <div class="underscore"></div>
  <h2 class="mb-s">{name}</h2>
 <!-- ^^^^^^^^^^^^ -->
  <img class="project-image" src={projectImageUrl} alt="" />
  <div class="project-container mt-m">
                           <!-- ^^^^ -->
    <div class="meta-data">
      <h3 class="semi-bold">Date</h3>
     <!-- ^^^^^^^^^^^^^^^^^ -->
      <p>{dateAccomplished}</p>
      <h3 class="semi-bold mt-m">Tech Stack</h3>
     <!-- ^^^^^^^^^^^^^^^^^^^^^^ -->
      <ul>
        {#each stack as skill}
          <li>{skill}</li>
        {/each}
      </ul>
    </div>
    <div class="project-content">
      {#each content as block}
        {#if block.type === 'text'}
          <svelte:element this={getTagFromStyle(block.style)}>{block.textToRender}</svelte:element>
        {:else}
          <img class="content-image" src={block.url} alt="" />
        {/if}
      {/each}
         
    </div>
  </div>
</main>

<!-- for more specific styling, you can write your own CSS below -->
<style>
  .work-page {
    padding-top: 80px;
    padding-bottom: 140px;
  }
  .project-image {
    width: 100%;  /* occupy full width of the parent */
    max-height: 450px; /* this is for layout purpose, it might cut the image */
    object-fit: cover;
  }
  .project-container {
    display: flex;
  }
  .meta-data {
    min-width: 200px; /* this is a trick to make one of the flex item to have minimum width */
  }
  .content-image {
    width: 100%;  /* occupy full width of the parent, but no max-height here */
  }
</style>

```
