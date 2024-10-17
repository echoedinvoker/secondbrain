---
date: 2024-10-16
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Path Helpers

```bash
src/
└── app
    ├── topics
    │   └── [slug]
    │       └── posts
    │           └── [postId]
    │               └── page.tsx
    └── page.tsx

```
In the folder structure above, we may need to use the path in many components and server actions:

```tsx
// in some components
<Link href={`/topics/${slug}/posts/${postId}`}>
```
```ts
// in some server actions
revalidatepath(`/topics/${slug}/posts/${postId}`)
```
```bash
src/
└── app 
   ├── contents  # <------------------- for some reason, we may need to add a new folder here
    │   └── topics                    # then, we should update the path in many components and server actions
    │       └── [slug]                # It can be a pain to update all of them...
    │           └── posts
    │               └── [postId]
    │                   └── page.tsx
    └── page.tsx

```

So we can create helper functions in a file to avoid above pain:

```ts
// src/paths.ts
const paths = {
  homePage() {
    return '/'
      },
  },
  topicShowPath(slug: string) {
    return `/topics/${slug}`
  },
  postCreatePath(slug: string) {
    return `/topics/${slug}/posts/new`
  },
  // ...
}

```
