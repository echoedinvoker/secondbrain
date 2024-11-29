---
date: 2024-11-04
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Chapter preview styling

After [[2024-11-04_Chapter-preview-HTML-structure-of-landing-page|Chapter preview HTML structure of landing page]], we want to do some styling work.


```svelte src/lib/components/ChapterPreview.svelte

<section class="chapter-preview">
  <h2 class="mb-l">What you're getting</h2>
  <div class="chapter-container">
    <ul>
<!-- ^^  only on `ul` tag, so don't need specific class name for it -->
      <li>
        <button
          class="chapter-title selected-chapter-title"
          aria-controls="chapter-info-1"
          aria-expanded="true"
        >
          <h3>Chapter 1: Navigting the Spanish bureaucracy</h3>
        </button>
      </li>
      <li>
        <button class="chapter-title" aria-controls="chapter-info-2" aria-expanded="false">
          <h3>Chapter 2: Legal requirements for moving to Spain</h3>
        </button>
      </li>
      <li>
        <button class="chapter-title" aria-controls="chapter-info-3" aria-expanded="false">
          <h3>Chapter 3: Finding a place to live in Spain</h3>
        </button>
      </li>
    </ul>
    <div class="chapter-info">
      <h3 class="chapter-strapline italic mb-s">
        Understanding Visas, Residencia, and Legal Essentials
      </h3>
      <p>
        Moving to Spain is a dream for many, but the reality of navigating the Spanish bureaucracy
        can be a nightmare. In this chapter, we'll walk you through the essential legal requirements
        for moving to Spain, from visas to residencia and everything in between so you can start
        your new life in Spain with confidence. We'll also share some tips and tricks for making the
        process as smooth as possible. Moving to Spain is a dream for many, but the reality of
        navigating the Spanish bureaucracy can be a nightmare. In this chapter, we'll walk you
        through the essential legal requirements for moving to Spain, from visas to residencia and
        everything in between so you can start your new life in Spain with confidence. We'll also
        share some tips and tricks for making the process as smooth as possible.
      </p>
    </div>
  </div>
</section>

<style>
  .chapter-preview {
    padding: 80px 0;
    margin-left: 12vw;       /* for paragraph spacing practice */
    margin-right: 20vw;      /* It is a good practice to have bigger right margin than left */
    max-width: 1150px;       /* 1150px is a common width for modern design */
  }

  .chapter-container {
    display: flex;
    justify-content: space-between;
  }

  .chapter-container ul {
    width: 40%;         /* 40% of the left container */
  }

  .chapter-info {
    width: 55%;         /* 55% of the right container, left 5% between two containers, which is appropriate space between */
  }

  .chapter-title {
    border-bottom: 1px solid grey;
    width: 100%;
    display: block;    /* button is inline-block by default, so we need to change it to block because we want to make it full width */
    padding: 12px;
    text-align: left;  /* because text of button is kind of paragraph, better to align it to left instead of center */
  }

  .selected-chapter-title {   /* this is for selected chapter, put following .chapter-title to override it */
    background-color: black;
    border: none;
    color: white;
    box-shadow:      /* here we use duo tone shadow, which is common in modern design */
            0 4px 6px rgba(0, 0, 0, 0.1),
            0 1px 3px rgba(0, 0, 0, 0.08);
  }
</style>
```
