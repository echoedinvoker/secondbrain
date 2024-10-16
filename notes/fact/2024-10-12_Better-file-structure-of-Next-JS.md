---
date: 2024-10-12
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Better file structure of Next JS


**Bad structure**

```bash
mattc@x ~/r/my-app (main)> tree src/
src/
├── app
│   ├── favicon.ico
│   ├── globals.css
│   ├── layout.tsx
│   ├── page.tsx
│   ├── components      # here we put all folders under app
│   ├── data            # hard to tell which fold is the route, which is just a folder
│   ├── utils
│   └── reliability
│       └── page.tsx
├── components
├── data
└── utils
```


**Better structure**

```bash
mattc@x ~/r/my-app (main)> tree src/
src/
├── app
│   ├── favicon.ico
│   ├── globals.css
│   ├── layout.tsx
│   ├── page.tsx
│   └── reliability   # Only put the route folders under app
│       └── page.tsx
├── components        # And put all normal folders outside of app
├── data
└── utils
```

