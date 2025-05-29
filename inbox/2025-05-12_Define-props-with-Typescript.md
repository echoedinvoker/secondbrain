---
date: 2025-05-12
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Define props with Typescript

Before we pass arguments to API `defineProps` to define the props:

```js
defineProps({
  currentTab: { type: String, required: true },
  ...
})
```

With Typescript, we can define the props by TypeScript interface:

```ts
defineProps<{
  currentTab: string
  ...
}>()
```

It's more readable and type-safe.

If we need to define complex props, we can write interfaces separately:

```ts
interface Tab {
  name: string;
  label: string;
}

interface Props {
  currentTab: string;
  tab: Tab;
}

defineProps<Props>()
```

