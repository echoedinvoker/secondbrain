---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# What makes a route DYNAMIC

| Description | Example |
|-------------|---------|
| Calling a 'dynamic function' or referencing a 'dynamic variable' when your route renders | - cookies.set() |
| | - cookies.delete() |
| | - useSearchParams() |
| | - searchParams prop |
| Assigning specific 'route segment config' options | - export const dynamic = 'force-dynamic' |
| | - export const revalidate = 0 |
| Calling 'fetch' and opting out of caching of the response | - fetch('...', { next: { revalidate: 0 } }); |
| Using a dynamic route | - /snippets/\[id]/page.tsx |
| | - /snippets/\[id]/edit/page.tsx |

There is no need to remember all the content above, just frequently use the `npm run build` command to check if the latest `build` result meets your expectations.
