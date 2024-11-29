---
date: 2024-11-02
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Fetcing Data with page.ts

Base on [[2024-11-02_Svelte-Data-Fetching-to-Frondend-diagram|Svelte Data Fetching to Frondend diagram]], we know that we have two options to fetch data in SvelteKit:
1. +page.server.ts for server side only, so it's mainly for secret data
2. other data better to use +page.ts for both server and client side

```bash
~/D/g/s/s/demo > tree src/routes/

src/routes/
├── blog
│   ├── [articleId]
│   │   ├── +page.svelte
│   │   └── +page.ts      # it will be executed on the server and client side
│   └── +page.svelte
└── +page.svelte

```

Using +page.ts to fetch data is actually almost the same as using +page.server.ts, the only difference is the type.

You can compare the +page.server.ts use case [[2024-11-02_Fetcing-Data-server-side-with-page.server.ts-file|here]]

```ts src/routes/blog/\[articleId\]/+page.ts 

import { error } from '@sveltejs/kit';
import type { PageLoad } from './$types';
//            ^^^^^^^^

export const load: PageLoad = async ({ params }) => {
//                 ^^^^^^^^ Only difference from +page.server.ts is type

  const blogArticles = [
    { id: '1', title: 'First article' },
    { id: '2', title: 'Second article' },
    { id: '3', title: 'Third article' },
  ]

  const article = blogArticles.find((article) => article.id === params.articleId);

  if (!article) {
    throw error(404, 'Article not found');
  }

  return {
    article,
  }
}

```

If data is not secret, it's better to use +page.ts because it can be executed on both server and client side and it's more flexible.

Even when we hover on the anchor tag, SvelteKit will execute the +page.ts file to fetch the data under the hood, which improves the user experience greatly.
