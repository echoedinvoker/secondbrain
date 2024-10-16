---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Query specific record and redner it

To implement this requirement, we need to use the [[2024-10-13_Dynamic-route|dynamic route]] to get the specifig `id` of the record from the URL, then query the record from the database and render it.

```bash
mattc@x ~/r/snippets (main)> tree src
src
├── app
│   ├── globals.css
│   ├── layout.tsx
│   ├── page.tsx
│   └── snippets
│       ├── [id]  # Dynamic route, `id` will be passed as a prop to the page.tsx with value from the URL
│       │   └── page.tsx
│       └── new
│           └── page.tsx
└── db
    └── index.ts

```

```tsx
// src/app/snippets/\[id\]/page.tsx 
import { db } from "@/db";

interface SnippetShowPageProps {
  params: { // dynamaic route params are passed as `params` in the props
    id: string;  // the value of `id` comes from the URL, so it's a string
  }
}

export default async function SnippetShowPage(props: SnippetShowPageProps) {
  const snippet = await db.snippet.findFirst({  // find the snippet with the id from the URL
    where: { id: parseInt(props.params.id) },  // if `id` of the snippet model is a number, we need to parse the `id` from the URL to a number, then the query will work
  })

  if (!snippet) { // if the snippet is not found, render a message
    return <div>Snippet not found</div>
  }

  return (
    <div>
      <h1>{snippet.title}</h1>
      <p>{snippet.code}</p>
    </div>
  )
}

```

In the above codes, we render a message when the snippet is not found. But Next.js provides a [[2024-10-13_Handle-query-not-found-case-elegantly-in-Next.js|better way]] to handle this case globally.
