---
date: 2024-10-18
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Issue of useSession and how to fix it

We must pay attention to the fact that because `useSession` is using request, we must do additional processing for the period when the result has not been obtained, instead of simply using true/false to generate the result.

**Bad practice:**

```tsx
'use client';

import { useSession } from "next-auth/react"

export default function Home() {
  const session = useSession();

  return (
    <div>
      {session.data?.user ? (
        <p>Authenticated</p>
      ) : (
        <p>Not authenticated</p>  {/* Even it is still loading, it will show this */}
      )}
    </div>
  )
}
```

**Good practice:**

```tsx
'use client';

import { useSession } from "next-auth/react"

export default function Home() {
  const { data: session, status } = useSession();
  if (status === "loading") {
    return <p>Loading...</p>;
  }
  return (
    <div>
      {session.status === "loading" ? (  // We can use session.status to check loading status
        <p>Loading...</p>                // Present correct message while loading
      ) : session.data?.user ? (
        <p>Authenticated</p>
      ) : (
        <p>Not authenticated
    </div>
  )
}
```
