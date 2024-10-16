---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Handle query not found case elegantly in Next.js

In Next.js, when you query for a resource and it's not found, you can handle this case by navigating to the 404 page using the `notFound` function from `next/navigation`.

```tsx
import { notFound } from "next/navigation"; // Next.js provides a notFound function to navigate to the 404 page

export default async function SnippetShowPage(props: SnippetShowPageProps) {
  const snippet = await db.snippet.findFirst({
    where: { id: parseInt(props.params.id) },
  })

  if (!snippet) {
    return notFound();  // return and call it to navigate to the 404 page
  }

  return (
    <div>
      <h1>{snippet.title}</h1>
      <p>{snippet.code}</p>
    </div>
  )
}

```

**Default or custom 404 page**

There is a default 404 page in Next.js, but you can also [[2024-10-13_Custom-not-found-page|create a custom 404 page]] by creating a file named `not-found.tsx` in the `pages` directory.
