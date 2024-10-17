---
date: 2024-10-14
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# How to save the codes of importing

```bash
src/
├── actions
│   └── actions.tsx  
└── app
    └── page.tsx
```

```tsx
import { createSnippet, editSnippet } from '../actions/actions';
```

You can change the file name `actions.tsx` to `index.tsx`, it can save the codes of importing.

```bash
src/
├── actions
│   └── index.tsx   # change the file name to index.tsx
└── app
    └── page.tsx
```

```tsx
import { createSnippet, editSnippet } from '../actions';  // only need to type the folder name
```

If there are many functions in the `actions.tsx` you wanna import, you can replace above codes with below codes.

```tsx
import * as actions from '../actions';   // This will save a lots of codes of importing
```

```
