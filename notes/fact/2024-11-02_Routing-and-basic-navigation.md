---
date: 2024-11-02
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Routing and basic navigation


```bash
~/D/g/s/s/demo > tree src/routes/

src/routes/  # `routes` is a special folder in SvelteKit, such as `app` in Next.js
├── blog  # under `routes` folder, each folder is a route
│   └── +page.svelte
└── +page.svelte  # +page.svelte is the entry point of the route, similar to `pages.tsx` in Next.js

```

```svelte src/routes/+page.svelte

<a href="/blog">Blog</a>
<!-- we can use the `a` tag to navigate to other page -->

```

```svelte src/routes/blog/+page.svelte 

<h1>Blog</h1>

```

>+page.svelte will only be considered as the entry point for that route when there is no +layout.svelte under the same route. This point should be noted carefully.

If there is a +layout.svelte in the route, check [[2024-10-12_Layout|layout]] for more information.
```bash
