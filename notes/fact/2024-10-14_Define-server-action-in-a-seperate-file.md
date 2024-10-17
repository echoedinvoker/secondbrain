---
date: 2024-10-14
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Define server action in a seperate file

We can define server actions in a separate file instead of the server component. And then import the actions from the file to the server component or the client component.

**Benefits:**
1. Centralized location for all server actions, making it easier to manage.
2. Keeps the server component clean and focused on the server logic.
3. only need to type `use server` once at the top of the file to tell Next.js that all the functions in this file are server actions.

```ts
// server-actions.ts

'use server';  // tell Next.js all the functions in this file are server actions

export function getServerTime() {
  return new Date().toISOString();
}

export function getServerVersion() {
  return process.env.NEXT_PUBLIC_VERSION;
}

// ...  other server actions

```

```tsx
// client-component.tsx

'use client';

import { getServerTime, getServerVersion } from './server-actions';  // import server actions

export default function ClientComponent() {
  const serverTime = getServerTime();  // call server action
  const serverVersion = getServerVersion();  // call server action
  return (
    <div>
      <p>Server time: {serverTime}</p>
      <p>Server version: {serverVersion}</p>
    </div>
  );
}
```
```tsx
```
