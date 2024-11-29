---
date: 2024-11-03
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Duo Hero Section layout Setting

This topic is following [[2024-11-03_Hero-section-component-example|Hero section component example]], here we will set the layout for the two hero sections.

```svelte src/lib/components/HeroSection.svelte

<section class="hero">
  <div class="hero-showcase text-center bold">

    <!-- Hero Showcase -->

  </div>
  <div class="hero-text white text-center">

    <!-- Hero Text -->
      
  </div>
</section>

<style>
  .hero {
    display: flex;  /* to make two hero side by side */
    height: 100vh;  /* full height of the viewport */
    overflow-y: hidden;
  }

  .hero-showcase {
    width: 50%;
    padding: 100px 16px 0 16px;  /* top right bottom left */
    background: linear-gradient( /* Using linear gradient for background color is common way on hero section */
      135deg,
      #e2cbff,
      #e2cbff 25%,
      #deeaff 50%,
      #ece5ff 75%,
      #927bee
    );
  }

  .hero-text {
    width: 50%;
    padding: 100px 5%;   /* top horizontal */
    background-color: black;
  }
</style>

```

Next, we need to style the decoration images in the left-side hero section. This is covered in [[2024-11-03_Dua-Hero-decorations-images-styling|Dua Hero decorations images styling]].

