---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Prisma client to update a record


Here is an example of using the Prisma client to update records in a server action:

```ts
// src/actions/index.tsx 
'use server';

import { db } from "@/db";
import { redirect } from "next/navigation";

export async function editSnippet(id: number, code: string) {
  await db.snippet.update({
    where: { id },
    data: { code }
  })
  redirect(`/snippets/${id}`);
}

```

