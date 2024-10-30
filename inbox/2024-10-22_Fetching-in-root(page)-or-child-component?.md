---
date: 2024-10-22
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Fetching in root(page) or child component?

```bash
mattc@x ~/r/discuss (main)> tree src
src
├── app
│   ├── page.tsx  # using PostList component to show posts with different topics
│   └── topics
│       └── [slug]
│           └── page.tsx # using PostList component to show posts with the same topic
└── components
    └── posts
        └── post-list.tsx # used in both page.tsx and topics/[slug]/page.tsx

```

>Should we fetch data in page.tsx or post-list.tsx?


## Option 1: Fetch in page.tsx

### Advantages ✅
- Easy to see what data a route needs to be displayed (all queries are in one place)
- Easier to make the child component reusable (because child just accept props and render)
- Easier to avoid 'n+1' query issues

### Disadvantages ❌
- Can lead to overfetching of data (this is for reusability of child component, it costs a bit more performance)
- Can lead to duplicate code in other Pages using the child component (do the same query in page.tsx)
- Sometimes annoying to write the interface for complex query data (type props of child will be more complex, and if page query changes, you need to change the interface in child component also, it might be a bit annoying)

>Fetching in the parent component is slower.


## Option 2: Fetch in child component

### Advantages ✅
- Easier to build 'skeleton' loading pages

### Disadvantages ❌
- Child components implementation is *locked in* (if you write a specific query in the child component, it's hard to reuse it in another page)

>Fetching in the child component is faster.


## Option 1.5 (recommended)

there is a middle ground between fetching in the root and child component. It's better than fetching in the root or child component, you can check it [[2024-10-22_Fetching-in-the-middle-of-root(page)-and-child-component|here]]
