---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Loading page

`loding.tsx` is a [[2024-10-13_Special-file-name-under-the-app-directory|special file name of the app]] . It will be rendered whenever the page is loading. 

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
│       │   ├── loading.tsx  # Loading page
│       │   ├── not-found.tsx
│       │   └── page.tsx  # Whenever the page is loading, `loading.tsx` will be rendered
│       ├── layout.tsx
│       └── new
│           └── page.tsx # No loading page for this one
└── db
    └── index.ts

```

```tsx
// src/app/snippets/\[id\]/loading.tsx 
export default function SnippetShowLoadingPage() {
  return (
    <div>Loading...</div>
  );
}

```

**Effect scope**

Its effect scope is the same as [[2024-10-13_Custom-not-found-page|not found page]], but we don't need to trigger it by calling function mamually as we do for the not found page.

