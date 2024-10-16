---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Run database CRUD in the action function

Database CRUD operations must be executed in the action function in the backend, not in the frontend, because it is a sensitive operation that needs to be executed on the server side.

```tsx
import { db } from "@/db";  // Import the Prisma client

export default function SnippetCreatePage() {
  async function createSnippet(formData: FormData) {
    'use server'
    const title = formData.get('title') as string;
    const code = formData.get('code') as string;

    const snippet = await db.snippet.create({  // Create a new snippet with the Prisma client, this is a CRUD operation and asynchronous method
      data: {    // Pass the data to be created, `data` field is built-in to POST payload
        title,
        code
      }
    });

    console.log(snippet);
  }
  return (
    <form action={createSnippet}>
      <h3 className="font-bold m-3">Create a new snippet</h3>
      <div className="flex flex-col gap-4">
        <div className="flex gap-4">
          <label className="w-12" htmlFor="title">Title</label>
          <input name="title" className="border rounded p-2 w-full" id="title" />
        </div>
        <div className="flex gap-4">
          <label className="w-12" htmlFor="code">Code</label>
          <textarea name="code" className="border rounded p-2 w-full" id="code" />
        </div>

        <button type="submit" className="bg-blue-200 p-2 rounded">Create</button>
      </div>
    </form>
  );
}

```

