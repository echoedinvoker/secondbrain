---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Redirect in the action function

After the backend work in the action function is completed, it often requires redirecting the user to another page. At this time, we usually use the `redirect` function provided by the 'next/navigation' library.

```tsx
import { db } from "@/db";
import { redirect } from "next/navigation";

export default function SnippetCreatePage() {
  async function createSnippet(formData: FormData) {
    'use server'
    ...

    redirect('/')  // Redirect to the home page, works only on the backend
  }
  return (
    <form action={createSnippet}>
      ...
    </form>
  );
}

```

**redirect**

`redirect` is a backend-only function that can only be used in the action function. It actually make server send a 302 redirect response to the client, and the client will automatically redirect to the specified URL.


**It raises an special error**

`redirect` function actually raises a special error that must be caught by the server in order to redirect correctly. So it must not be put inside a try-catch block, otherwise the redirection will not be done correctly.
