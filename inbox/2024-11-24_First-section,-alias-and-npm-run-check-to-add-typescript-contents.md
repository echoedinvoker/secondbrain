---
date: 2024-11-24
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# First section, alias and npm run check to add typescript contents

```js
// svelte.config.js

import adapter from '@sveltejs/adapter-auto';
import { vitePreprocess } from '@sveltejs/vite-plugin-svelte';

/** @type {import('@sveltejs/kit').Config} */
const config = {
  preprocess: vitePreprocess(),

  kit: {
    adapter: adapter(),

    // add alias to src/lib/components
    alias: {
      $components: 'src/lib/components',
    }
  }
};

export default config;

```

```bash
~/D/g/s/s/dev-portfolio > tree src/
src/
├── app.css
├── app.d.ts
├── app.html
├── lib
│   ├── components # add components folder and its two files
│   │   ├── HeroSection.svelte # we will write its contents later...
│   │   └── index.ts # export { default as HeroSection } from './HeroSection.svelte';
│   └── index.ts
└── routes
    ├── +layout.svelte
    └── +page.svelte

```

Render the `HeroSection` component in the root page:

```svelte
<!-- src/routes/+page.svelte  -->

<script lang="ts">
  import { HeroSection } from "$components";
  //                          ^^^^^^^^^^^^^ here may has tsc error, use `npm run check`,
  //                                        it will scan whole project and add type definitions to .svelte-kit/types/
</script>

<HeroSection />

```

Create structure of `HeroSection` component:

```svelte
<!-- src/lib/components/HeroSection.svelte -->

<section class="hero-section">
  <h3>Hello! I'm Matt</h3>
  <h1>I'm a Svelte developer crafting intuitive and performant</h1>
  <h1>web experiences.</h1>
  <button>Let's talk!</button>
</section>

```

Add some styles to `HeroSection` and we will use some utility classes from `app.css`:

```css
.dark-grey {
  color: #5e5e5e;
}

/* This is for all sectino, to make them has same margin and max-width. */
.default-margin {
  margin-left: 15vw;
  margin-right: 20vw;
  max-width: 950px;
}

/* design to directly use on a empty div to make a line. */
.underscore {
  background-color: #5e5e5e;
  height: 4px;
  width: 40px;
}

.mt-m {
  margin-top: 32px;
}

```

```svelte
<!-- src/lib/components/HeroSection.svelte -->

<section class="hero-section default-margin">
                        <!-- ^^^^^^^^^^^^^^ -->
  <h3>Hello! I'm Matt</h3>

  <!-- create a line by using utility class `underscore` with empty div -->
  <div class="underscore mb-m"></div>

  <h1>I'm a Svelte developer crafting intuitive and performant</h1>
  <h1 class="dark-grey">web experiences.</h1>
<!-- ^^^^^^^^^^^^^^^^^ to make our h1 text not so boring, we changed portion of h1 text to dark-grey -->

  <button>Let's talk!</button>
 <!-- ^^^^^^^^^^^^^^^^^^^^^^^ we will create a generic component for button, so no need to do any styling here. -->
</section>

<!-- default-margin only add margin left and right, so we need to add padding-top to make it not stick to the top of the page. -->
<style>
  .hero-section {
    padding-top: 60px;
  }
</style>

```
