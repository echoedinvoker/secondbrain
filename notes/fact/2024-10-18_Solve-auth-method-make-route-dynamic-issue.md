---
date: 2024-10-18
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Solve auth method make route dynamic issue

```tsx
import { auth } from "@/auth"
import * as actions from "@/actions"

export default function Profile() {
  const session = auth()  // auth use cookie, so it will make route dynamic
  return (
    <div>
      {session?.user ? (
        <div>
          <Avatar src={session.user.image} />
        </div>
      ) : (
        <div>
          <form actoin={actions.signIn}
            <button type="submit">Sign in</button>
          </form>
        </div>
      )}
    </div>
  )
}
```

To solve this issue, we can turn this component to client component (or split auth part to child component) and use `useSession` hook to get session data. `useSession` handle authenticaion by requesting to backend server, so it will not make route dynamic.

```tsx
'use client'

import { useSession } from "@/auth"  // instead of auth, use useSession
import * as actions from "@/actions"

export default function Profile() {
  const session = useSession()  // use useSession hook, it won't make route dynamic
  return (                      // because it requests to backend server instead of using cookie
    <div>
      {session.data?.user ? (   // be careful with the difference between session and session.data
        <div>
          <Avatar src={session.data.user.image} />
        </div>
      ) : (
        <div>
          <form actoin={actions.signIn}
            <button type="submit">Sign in</button>
          </form>
        </div>
      )}
    </div>
  )
}
```

**But there is another issue because of useSession hook**

Because the request to the backend server is asynchronous, [[2024-10-18_Issue-of-useSession-and-how-to-fix-it|another issue]] has arisen in this example that needs to be resolved.
