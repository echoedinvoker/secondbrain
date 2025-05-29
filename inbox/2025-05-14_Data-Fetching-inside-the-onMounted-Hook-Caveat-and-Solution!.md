---
date: 2025-05-14
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Data Fetching inside the onMounted Hook Caveat and Solution!

If there is a route as below:
```ts
{
    path: '/recipe/:id',
    name: 'recipe',
    component: () => import('@/views/Recipe.vue'),
}
```

When we navigate to `/recipe/2` from `/recipe/1`, the `onMounted` hook in `Recipe.vue` will not be called again because the component is already mounted.

In this case, we should replace the `onMounted` hook with the `watch` hook with `immediate: true` to ensure that the data fetching logic is executed whenever the route changes.

```ts
//onMounted(() => {
//    fetchData(route.params.id);
//});

watch(
    () => route.params.id, // because route.params.id is not reactive,
                           // so we need to turn it into a getter
    (newId) => {
        fetchData(newId);
    },
    { immediate: true } // this will call the function when the component is mounted, or watch only works when the value changes
);
```

