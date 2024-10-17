---
date: 2024-10-16
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# See if user is signed in

Not like [[2024-10-16_Make-server-actions-to-signIn-and-signOut|signIn and signOut]]  functions, checking if the user is signed in is different in the server component and client component.

**server component**

```tsx
import { auth } from '@/auth';

export default async function ServerComponent() {
  const session = await auth();

  if (session?.user) {
    // user is logged in
  } else {
    // user is not logged in
  }
}
```

**client component**

```tsx
'use client';

import { SessionProvider } from "next-auth/react"; // client component must be wrapped in SessionProvider
                                                   // to use useSession hook
interface Props {
  children: React.ReactNode;
}

export default function ClientProviderComponent({ children }: Props) {
  return (
    <SessionProvider>
      <ClientComponent />
    </SessionProvider>
  );
}

```

```tsx
'use client';

import { useSession } from "next-auth/react";

export default function ClientComponent() {
  const session = useSession();  // instead of auth() in the server component,
                                // use hook useSession in the client component

  if (session.data?.user) {  // slightly different from the server component, be careful
    // user is logged in
  } else {
    // user is not logged in
  }
}
```
