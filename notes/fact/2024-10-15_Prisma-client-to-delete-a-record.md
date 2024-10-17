---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Prisma client to delete a record

Here is an example of using the Prisma client to delete a record in a server action:

```ts
'use server';

import { db } from "@/db";
import { redirect } from "next/navigation";

export async function deleteSnippet(id: number) {
  await db.snippet.delete({
    where: { id }
  })

  redirect("/");
}

```

