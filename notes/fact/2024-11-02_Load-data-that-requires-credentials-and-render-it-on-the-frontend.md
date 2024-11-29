---
date: 2024-11-02
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Load data that requires credentials and render it on the frontend


```bash
~/D/g/s/s/demo > tree src/routes/

src/routes/
├── blog
│   ├── [articleId]  # dynamic route
│   │   ├── +page.server.ts  # server-side processing file for +page.svelte
│   │   └── +page.svelte
│   └── +page.svelte
└── +page.svelte

```

+page.server.ts is server side processing file for +page.svelte, it is used to fetch data that requires credentials or any other server side processing before rendering it on the frontend.

```ts src/routes/blog/\[articleId\]/+page.server.ts 

import { error } from '@sveltejs/kit';
import type { PageServerLoad } from './$types';  // NOTE: $types must come from CURRENT folder

export const load: PageServerLoad = async ({ params }) => {   // `load: PageServerLoad` is a fixed name and type for +page.server.ts, do not change it
//                                         ^^^^^^^^^^ we can get dynamic route params from here

  // this block should be replaced with actual data fetching logic, here is just a dummy data for demonstration
  const blogArticles = [
    { id: '1', title: 'First article' },
    { id: '2', title: 'Second article' },
    { id: '3', title: 'Third article' },
  ]

  const article = blogArticles.find((article) => article.id === params.articleId);
  //                                                            ^^^^^^^^^^^^^^^^ get dynamic route [articleId]'s value
  // end of dummy

  if (!article) {
    throw error(404, 'Article not found');  // this is SvelteKit server side function to redirect to 404 page with message
  }

  return {       // return data that will be passed to +page.ts
    article,     // if there is no +page.ts in this route, it will be passed to +page.svelte directly
  }
}

```

Because in this example, there is no +page.ts in this route, so the returned data of +page.server.ts will be passed to +page.svelte directly. You can check [[2024-11-02_Svelte-Data-Fetching-to-Frondend-diagram|here]] to know more about data fetching flow in SvelteKit.

```svelte src/routes/blog/+page.svelte 

```svelte src/routes/blog/\[articleId\]/+page.svelte 

<script lang="ts">
  const props = $props();

  $inspect(props);
  // {
  //   data: {                        // returned data from +page.server.ts will go to props.data
  //     article: { 
  //       id: '1',
  //       title: 'First article'
  //     },
  //   },
  //   form: null
  // }
</script>

<p>{props.data.article.title}</p>

```
