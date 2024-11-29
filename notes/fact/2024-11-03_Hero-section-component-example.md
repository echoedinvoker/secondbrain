---
date: 2024-11-03
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Hero section component example

```svelte src/routes/+page.svelte

<script>
  import { HeroSection } from '$components';
</script>

<HeroSection />

```

```svelte src/lib/components/HeroSection.svelte 

<script lang="ts">
  import phoneCover from '$assets/phone_cover.png';
  import bookCover from '$assets/book_cover.png';
</script>

<section class="hero">
<!--            ^^^^    -->
  <div class="heor-showcase text-center bold">
<!--          ^^^^^^^^^^^^^   these classes are specific to this hero section, we can use them before actual CSS is written -->
<!--                          because it will also help us to understand the structure of the component -->
<!--                          other classes are global and can be used in other components as well -->
<!--                          they should be defined in the global CSS file -->

    <!-- use multiple h1 tags to create a hero title, and last one with a margin-bottom -->
    <h1 class="purple">MOVE TO</h1>
    <h1 class="purple mb-xs">SPAIN</h1>
    <!-- end of hero title -->

    <!-- after hero title, add a subtitle or a paragraph -->
    <h4 class="italic semi-bold">There's no need to make the same mistakes I made.</h4>

    <!-- decorative images -->
    <img class="book-cover" src={bookCover} alt="" />
    <img class="phone-cover" src={phoneCover} alt="" />
  </div>

  <!-- This sample is grouped with two hero patterns into one section -->
  <div class="hero-text white text-center">
    <h1>THIS EBOOK WILL</h1>
    <h1>SAVE YOUR MONEY,</h1>
    <h1 class="mb-m">TIME & NERVES</h1>
    <p class="light-grey">
      I moved to Spain in 2017 and I made a lot of mistakes. I lost a lot of money, time and nerves.
      I don't want you to make the same mistakes. That's why I wrote this ebook. It will save you a
      lot of money, time and nerves. It will help you to move to Spain smoothly and without any
      problems.
    </p>
  </div>
</section>

```

Bellow only list global CSS styles we use in this component:

```css src/app.css

/* ... */

body {
  font-family: "Open Sans", sans-serif;
}

/* ... */

h1 {
  font-size: 100px;
  font-family: "Anton", sans-serif;
  line-height: 1.1;
  /* Adjust as needed for spacing */
  font-weight: bold;
}

/* ... */

h4 {
  font-family: "Anton", sans-serif;
  font-size: 20px;
  line-height: 1.3;
  font-weight: normal;
}

p,
li {
  font-size: 20px;
  line-height: 1.4;
}

h1,
h2,
h3,
h4,
h5,
h6,
p {
  margin: 0;
}

/* UTILITY CLASSES */

/* ... */

.mb-xs {
  margin-bottom: 8px;
}

.mb-s {
  margin-bottom: 16px;
}

.mb-m {
  margin-bottom: 32px;
}

.mb-l {
  margin-bottom: 64px;
}

.bold {
  font-weight: 800;
}

.semi-bold {
  font-weight: 500;
}

.italic {
  font-style: italic;
}

.text-center {
  text-align: center;
}

.purple {
  color: #3233a6;
}

.white {
  color: white;
}

.light-grey {
  color: #aaaaaa;
}

```


