---
date: 2024-10-16
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Create route for communating with Github

After [[2024-10-16_Setup-NextAuth-in-Nextjs-project|setting up Next-Auth]] , if our provider is Github, we need to create a route in the Nextjs app for Github to communicate with the Nextjs app.
Here I use [[2024-10-13_Special-file-name-under-the-app-directory|special file name]] `route.ts` to define the API endpoints.

```bash
mattc@x ~/r/discuss (main)> tree src/
src/
├── app
│   ├── api
│   │   └── auth
│   │       └── [...nextauth]
│   │           └── route.ts   # `route.ts` is a special file name, 
│   ├── favicon.ico            #  export function named `GET`, `POST`, `PUT`, `DELETE` etc.
│   ├── globals.css            #  then it will provide endpoints for outside world to communicate with the app.
│   ├── layout.tsx
│   ├── page.tsx
│   └── providers.tsx
├── auth.ts  # NextAuth here already provide GET, POST functions, we just need to export them in above `route.ts`
└── db
    └── index.ts

```

```tsx
// src/app/api/auth/\[...nextauth\]/route.ts
export { GET, POST } from '@/auth'

```
