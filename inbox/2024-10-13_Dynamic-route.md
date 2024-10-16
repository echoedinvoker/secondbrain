---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Dynamic route

```bash
mattc@x ~/r/snippets (main)> tree src
src
├── app
    ├── page.tsx
    └── snippets
        ├── [id]  # Dynamic route
        │   └── page.tsx  # `id` will be passed as a prop to the page.tsx with value from the URL
        └── new
            └── page.tsx

```

```bash
mattc@x ~/r/snippets (main)> tree src
src
├── app
    ├── page.tsx
    └── snippets
        ├── [id]
        │   ├── page.tsx
        │   └── edit
        │       └── page.tsx  # this file also receives `id` as a prop under the `params` key
        └── new
            └── page.tsx

```

```bash
mattc@x ~/r/snippets (main)> tree src
src
├── app
    ├── page.tsx
    └── snippets
        ├── [id]
        │   ├── page.tsx
        │   └── edit
        │       ├── page.tsx
        │       └── [status]
        │           └── page.tsx  # this file receives `id` and `status` as props under the `params` key
        └── new
            └── page.tsx

```

