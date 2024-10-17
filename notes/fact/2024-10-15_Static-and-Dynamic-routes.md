---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Static and Dynamic routes

```bash
mattc@x ~/r/snippets (main)> npm run build

...

Route (app)                              Size     First Load JS
┌ ○ /                                    178 B          94.1 kB
├ ○ /_not-found                          875 B            88 kB
├ ƒ /snippets/[id]                       178 B          94.1 kB
├ ƒ /snippets/[id]/edit                  4.7 kB         91.8 kB
└ ○ /snippets/new                        767 B          87.9 kB
+ First Load JS shared by all            87.1 kB
  ├ chunks/117-594a467894e41764.js       31.6 kB
  ├ chunks/fd9d1056-7d9b70e9456442b8.js  53.7 kB
  └ other shared chunks (total)          1.88 kB


○  (Static)   prerendered as static content
ƒ  (Dynamic)  server-rendered on demand

```

**Static routes**

Static routes will use the [[2024-10-15_Full-Route-Cache|Full Route Cache]], so during this build process, a snapshot HTML file of the static route will be generated. Each time a user requests it, the snapshot file will be returned directly without needing to render it again.

All routes are static by default.


**Dynamic routes**

Similar to traditional routes, when receiving a request from the user, it will dynamically render. These routes will not be cached, and will be re-rendered each time a request is made.


**Which one is better?**

Static routes are very fast because they do not require re-rendering the page when user request, but if the data changes, they cannot dynamically update the data on the page. Dynamic routes, on the other hand, can dynamically update data, but they are slower. Therefore, both types of routes have their own advantages and disadvantages, and it is important to choose based on your own needs.

