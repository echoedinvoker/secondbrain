---
date: 2024-12-07
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Hero section styling of Book Nest

After complete [[2024-12-07_Hero-section-structure-of-Book-Nest|the Hero section structure of Book Nest]], we can now style it:

```svelte
<!-- src/lib/components/HeroSection.svelte  -->

<script lang="ts">
  import heroImage from '$assets/hero.png';
</script>

<section class="hero default-margin">
                <!-- ^^^^^^^^^^^^^^ 0 13vw 0 13vw; -->
  <div class="hero-text">
    <h1>Book Nest</h1>
    <h3>Your personal book library.</h3>
    <h4 class="mt-l">Create your very own digital library where you can keep track of every book you own, read and love.</h4>
   <!-- ^^^^^^^^^^^^ -->
    <h4 class="mb-s">Our app offers a beautifully designed, easy-to-use interface that makes manaing your book collection a joy.</h4>
   <!-- ^^^^^^^^^^^^ -->

    <button>Sign up</button>  <!-- we will create generic button component later -->

  </div>
  <img src={heroImage} alt="" class="hero-image">
</section>

<!-- write the specific styles below: -->
<style>
  .hero {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 100px;
    margin-bottom: 80px;
  }
  .hero-image {
    width: 40%;
  }
  .hero-text {
    width: 55%; /* with space-between and 40% for image, the space between is 5% */

    /* inside hero-text, we also use flexbox to align headlines and button */
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    padding-right: 50px;  /* for paragraph text to the border, adding some padding is good practice */
  }

</style>


```
