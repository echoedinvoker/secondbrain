---
date: 2024-10-12
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Better way to import in the deep component

```bash
mattc@x ~/r/my-app (main)> tree src/
src/
├── components
│   └── header.tsx      # let's say this component need to be imported in all page.tsx
├── app
│   ├── favicon.ico
│   ├── globals.css
│   ├── layout.tsx
│   ├── page.tsx                # import ../components/header.tsx
│   └── reliability
│       ├── data
│       │   ├── page.tsx        # import ../../components/header.tsx
│       │   └── stats
│       │       └── page.tsx    # import ../../../components/header.tsx
│       └── page.tsx
├── data
└── utils
```

As you can see, the `header.tsx` is imported in multiple places. The current way to import it is a bit messy.
We can use the `@` alias to import it from the root directory.

```bash
mattc@x ~/r/my-app (main)> tree src/
src/
├── components
│   └── header.tsx      # let's say this component need to be imported in all page.tsx
├── app
│   ├── favicon.ico
│   ├── globals.css
│   ├── layout.tsx
│   ├── page.tsx                # import @/components/header.tsx   # @ is the root directory
│   └── reliability
│       ├── data
│       │   ├── page.tsx        # import @/components/header.tsx
│       │   └── stats
│       │       └── page.tsx    # import @/components/header.tsx
│       └── page.tsx
├── data
└── utils
```

No matter how deep the component is, you can import it from the root directory. It's a better way to manage imports in the deep component.
