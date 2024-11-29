---
date: 2024-11-04
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Chapter preview HTML structure of landing page

This is middle part of landing page, following by [[2024-11-03_Hero-Section:-HTML-Structure|hero Section]].

When we are in the initial stage of building the HTML structure, we do not need to consider styles, only the structure and semantics.


```svelte src/lib/components/ChapterPreview.svelte

<section class="chapter-preview">
<!--            ^^^^^^^^^^^^^^^ not only semantic tag `section`, we also add meaningful class name to let developers know what this section is about -->
  <h2>What you're getting</h2>
  <div class="chapter-container">
<!--          ^^^^^^^^^^^^^^^^^ when using `div`, meaningful class name is more important to let developers know what this container is about -->
    <ul>
      <li>
<!--  ^^^^ even we can make `li` clickable, we should use `button` for accessibility -->
        <button
          class="chapter-title selected-chapter-title"
<!--                           ^^^^^^^^^^^^^^^^^^^^^^ for selected chapter only -->
          aria-controls="chapter-info-1" aria-expanded="true">
<!--      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ these are for people who use browser reader, increasing accessibility of our app -->
          <h3>Chapter 1: Navigting the Spanish bureaucracy</h3>
        </button>
      <li>
      </li>
				<button class="chapter-title" aria-controls="chapter-info-2" aria-expanded="false">
					<h3>Chapter 2: Legal requirements for moving to Spain</h3>
				</button>
      <li>
      </li>
				<button class="chapter-title" aria-controls="chapter-info-3" aria-expanded="false">
					<h3>Chapter 3: Finding a place to live in Spain</h3>
				</button>
      </li>
    </ul>
    <div class="chater-info">
      <h3>
        Understanding Visas, Residencia, and Legal Essentials
      </h3>
      <p>
        Moving to Spain is a dream for many, but the reality of navigating the Spanish bureaucracy can
        be a nightmare. In this chapter, we'll walk you through the essential legal requirements for
        moving to Spain, from visas to residencia and everything in between so you can start your new
        life in Spain with confidence. We'll also share some tips and tricks for making the process as
        smooth as possible. Moving to Spain is a dream for many, but the reality of navigating the
        Spanish bureaucracy can be a nightmare. In this chapter, we'll walk you through the essential
        legal requirements for moving to Spain, from visas to residencia and everything in between so
        you can start your new life in Spain with confidence. We'll also share some tips and tricks
        for making the process as smooth as possible.
      </p>
    </div>
  </div>
</section>

```

Next, we start to do some [[2024-11-04_Chapter-preview-styling|styling]].
