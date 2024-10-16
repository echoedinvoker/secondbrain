---
date: 2024-10-14
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Using Client component in a Server component

**Why?**

If we want to use hooks or event handlers, we need to use the Client component. Often, we want to use certain libraries, but they require hooks or event handlers to function properly.


**Why not just turn the Server component into a Client component?**

Our data may be obtained directly by querying the database. It will become very troublesome if we turn the Server component into a Client component.


**Example**

```bash
src/
├── app
│   ├── page.tsx
│   └── snippets
│       ├── [id]
│       │   ├── edit
│       │   │   └── page.tsx # this is a Server component, which need to use hooks or event handlers
│       │   └── page.tsx
│       └── new
│           └── page.tsx
├── components
│   └── snippet-edit-form.tsx # Client component usually located outside the app folder
└── db
    └── index.ts

```
```tsx
// src/app/snippets/\[id\]/edit/page.tsx 
import SnippetEditForm from "@/components/snippet-edit-form";
import { db } from "@/db";
import { notFound } from "next/navigation";

interface SnippetEditPageProps { params: { id: string; } }

export default async function SnippetEditPage(props: SnippetEditPageProps) {
  const id = parseInt(props.params.id);
  const snippet = await db.snippet.findFirst({ where: { id } });  // query snippet from database directly

  if (!snippet) { return notFound(); }

  return (
    <div>
      <SnippetEditForm snippet={snippet} /> {/* props quried data to the Client component */}
    </div>
  )
}

// src/components/snippet-edit-form.tsx 
'use client';  // tell Next.js to use Client component

import { Snippet } from "@prisma/client";  // we can get type from the model we defined in Prisma

interface SnippetEditFormProps { snippet: Snippet; }

export default function SnippetEditForm(props: SnippetEditFormProps) { // get the props from the Server component

  // use hooks or event handlers here
  
  return (
    <div>Edit Snippet {props.snippet.id}</div>
  )
}
```
**Client component will be on the serverd side at first request**

Be aware that the Client component will be on the server side at the first request. It will be on the client side after the first request. So, we should not use any browser-specific APIs in the Client component.
