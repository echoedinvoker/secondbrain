---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Custom not found page

File name `not-found.tsx` under the `app` is a special file that will be used when calling `notFound()`. It will find the closest `not-found.tsx` file in the parent directories or the same directory. If it can't find one, it will use the default not found page.

```bash
mattc@x ~/r/snippets (main)> tree src/
src/
├── app
│   ├── favicon.ico
│   ├── globals.css
│   ├── layout.tsx
│   ├── page.tsx
│   └── snippets
│       ├── [id]
│       │   ├── not-found.tsx # when notFound() is called, it will navigate to this page
│       │   └── page.tsx # Only calling notFound() in this file will navigate to the not-found.tsx above
│       ├── layout.tsx
│       └── new
│           └── page.tsx # calling notFound() in this file will navigate to the default not found page, not the custom one above
└── db
    └── index.ts

```

```tsx
// src/app/snippets/\[id\]/not-found.tsx 
export default function SnippetNotFound() {
  return (
    <div>
      <h1>Snippet not found</h1>
    </div>
  );
}


```
