---
date: 2024-11-03
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# How to layout decoration image

We prefer to use the `position: absolute` property to position the decoration images relative to the specific block.

```svelte src/lib/components/HeroSection.svelte

<script lang="ts">
  import phoneCover from '$assets/phone_cover.png';
  import bookCover from '$assets/book_cover.png';
</script>

<section class="hero">
  <div class="hero-showcase">

    <!-- ... -->

    <!-- decoration images -->
    <!-- the order of img tags is important, the last image will be on top of the others -->
    <!-- when screen size is small, the phone image and book image will overlap -->
    <img class="book-cover" src={bookCover} alt="" />
    <img class="phone-cover" src={phoneCover} alt="" />   

  </div>
</section>

<style>
  .hero-showcase {
    position: relative;  /* to position the image relative to the parent block */
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

**Trick**
- Use zero of certain direction to position with transform: translate property. We can cut the image from the that direction. 
- Order of the image is important. The last image will be on top of the others. 
