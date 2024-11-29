---
date: 2024-11-03
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Dua Hero decorations images styling

This topic is following [[2024-11-03_Duo-Hero-Section-layout-Setting|Duo Hero Section layout Setting]], here we will style the decoration images in the left-side hero section.

```svelte src/lib/components/HeroSection.svelte

<script lang="ts">
  import phoneCover from '$assets/phone_cover.png';
  import bookCover from '$assets/book_cover.png';
</script>

<section class="hero">
  <div class="hero-showcase text-center bold">

    <!-- text -->
    <h1 class="purple">MOVE TO</h1>
    <h1 class="purple mb-xs">SPAIN</h1>
    <h4 class="italic semi-bold">There's no need to make the same mistakes I made.</h4>
    <!-- end of text -->

    <!-- decoration images -->
    <!-- the order of img tags is important, the last image will be on top of the others -->
    <!-- when screen size is small, the phone image and book image will overlap -->
    <img class="book-cover" src={bookCover} alt="" />
    <img class="phone-cover" src={phoneCover} alt="" />   
    <!-- end of decoration images -->

  </div>
  <div class="hero-text white text-center">

    <!-- Hero Text -->

  </div>
</section>

<style>
  .hero {
    /* ... */
  }

  .hero-showcase {
    position: relative;  
    /* ... */
  }

  .hero-text {
    /* ... */
  }

  .book-cover {
    position: absolute;  /* to position the image relative to the parent block */
    right: 2%;           /* position from the right edge 2% */
    bottom: 0;           /* image bottom edge is at the bottom edge of the parent block */
    transform: translateY(30%);   /* let the image be 30% from the bottom edge, it means we can only see 70% of the image */
  }

  .phone-cover {
    position: absolute;  /* to position the image relative to the parent block */
    right: 52%;          /* position from the right edge 52% */
    bottom: 0;           /* image bottom edge is at the bottom edge of the parent block */
    transform: translateY(27%);   /* let the image be 27% from the bottom edge, it means we can only see 73% of the image */
  }
</style>

```

From above example, we can see [[2024-11-03_How-to-layout-decoration-image|How to layout decoration image]].
