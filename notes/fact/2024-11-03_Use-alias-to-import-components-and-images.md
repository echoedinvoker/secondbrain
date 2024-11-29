---
date: 2024-11-03
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Use alias to import components and images

```bash
~/D/g/s/s/ebook-landing > tree src
src
├── app.css
├── app.d.ts
├── app.html
├── assets
│   ├── book_cover.png     # image need to be imported
│   └── phone_cover.png    # image need to be imported
├── lib
│   └── components
│       └── HeroSection.svelte  # component need to be imported
└── routes
    └── +page.svelte  # page need above component and images

```

```svelte src/routes/+page.svelte 

<script>
  import HeroSection from './../lib/components/HeroSection.svelte';
//                        ^^^^^^
  import bookCover from './../assets/book_cover.png';
//                      ^^^^^^ it will be worse if we have to go up more levels
</script>

```

We can set alias to import components and images easily, and index.ts to export all components at once.

```js svelte.config.js 

//...

/** @type {import('@sveltejs/kit').Config} */
const config = {
  //...
  kit: {
    //...

    // set alias to import components and images easily
    alias: {
      $components: 'src/lib/components',
      $assets: 'src/assets',
    }
  }
};

export default config;
```

```bash

~/D/g/s/s/ebook-landing > tree src/

src/
├── app.css
├── app.d.ts
├── app.html
├── assets
│   ├── book_cover.png
│   └── phone_cover.png
├── lib
│   └── components
│       ├── HeroSection.svelte
│       └── index.ts           # add index.ts to export all components
└── routes
    ├── +layout.svelte
    └── +page.svelte

```

```ts src/lib/components/index.ts 

export { default as HeroSection } from './HeroSection.svelte';
```

```svelte src/routes/+page.svelte

<script>
  import bookCover from '$assets/book_cover.png';
//                       ^^^^^^^ use alias
  import { HeroSection } from '$components';   // because of index.ts, we can import all components from $components at once
//                             ^^^^^^^^^^^  use alias
</script>

<HeroSection />
<img src={bookCover} alt="" />

```
