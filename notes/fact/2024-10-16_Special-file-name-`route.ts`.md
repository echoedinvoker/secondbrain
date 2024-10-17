---
date: 2024-10-16
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Special file name `route.ts`

If you need to let outside world to communicate with your Next.js app, you can create a file named `route.ts` under `app` folder, it is one of [[2024-10-13_Special-file-name-under-the-app-directory|special file name under the app directory]].

```bash
src/
└── app
    └── api  # /api
        └── auth  # /api/auth
           └── [...nextauth]  # /api/auth/  and following any path will be handled by `route.ts` under this folder
               └── route.ts

```

Inside `route.ts`, we should export function with special name such as: GET, POST, PUT, DELETE, PATCH, ... etc.
The function name decides the HTTP method that the route will handle.

```ts
export function GET() {
  return {
    status: 200,
    body: { message: 'Hello World' },
  };
}
```

**Does Next.js frontend itself need to rely on this method to communicate with the backend?**

No, even though you can use this method to communicate with the backend, it is not recommended. Instead, you should use [[2024-10-13_Action-function|server action]] to communicate with the backend.
