---
date: 2024-10-16
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Install dependencies of next-auth

```bash
npm install --save-exact @auth/core@0.18.1 @auth/prisma-adapter@1.0.6 next-auth@5.0.0-beta.3
```

next-auth is known for frequently crashing due to updates, so we use `--save-exact` to install a specific version of the package.
