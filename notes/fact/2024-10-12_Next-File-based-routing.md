---
date: 2024-10-12
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Next File based routing

```bash
mattc@x ~/r/my-app (main)> tree src/
src/
└── app  # app is a very special folder in Next.js, directory names are treated as routes inside the app folder, thie path is root /
    ├── page.tsx  # page.tsx is a special file name in Next.js, it will be treated as a entry point for the route
    └── reliability  # /reliability
        ├── details   # /reliability/details   # nested route is also supported
        │   └── page.tsx
        └── page.tsx
```

