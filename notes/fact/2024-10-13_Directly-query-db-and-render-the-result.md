---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Directly query db and render the result

```tsx
import { db } from "@/db";  // prisma client instance

export default async function Home() {  // `async` function for `await` keyword
  const snippets = await db.snippet.findMany()   // directly query the database by prisma client method
  const renderSnippets = snippets.map(snippet => {  // map over the snippets and render them
    return (
      <div key={snippet.id}>
        <h2>{snippet.title}</h2>
        <p>{snippet.code}</p>
      </div>
    )
  })

  return (
    <div>{renderSnippets}</div>
  );
}

```
