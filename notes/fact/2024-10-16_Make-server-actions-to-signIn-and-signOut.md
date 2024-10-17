---
date: 2024-10-16
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Make server actions to signIn and signOut

signIn and signOut are methods provided by [[2024-10-16_Setup-NextAuth-in-Nextjs-project|Next-Auth]] , but since the login and logout status of users is also a piece of data that changes over time in a Next.js app, we will make them into [[2024-10-13_Action-function|server actions]] for easier management and understanding.

This is just an opinionated way of organizing code, and it is not necessary to follow it. However, it is a good practice to have a clear separation of concerns in your codebase.

```bash
src/
├── actions
│   └── index.ts  # wrap signIn and signOut methods as server actions here
├── app
│   ├── api
│   │   └── auth
│   │       └── [...nextauth]
│   │           └── route.ts
│   ├── globals.css
│   ├── layout.tsx
│   ├── page.tsx
│   └── providers.tsx
├── auth.ts        # signIn and signOut methods come from here
└── db
    └── index.ts

```

```ts
// src/actions/index.ts
'use server';

import * as auth from '@/auth';

export async function signIn() {
  return auth.signIn('github')  // signIn must choose a provider because we may have multiple providers
}

export async function signOut() {
  return auth.signOut()  // signOut does not need to specify a provider, just signOut
}

```

