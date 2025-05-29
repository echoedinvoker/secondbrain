---
date: 2025-05-14
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Route with RouterLink

In Vue.js, we use `RouterLink` to navigate between different routes in our application instead of using traditional anchor tags because it provides a way to handle client-side routing without causing a full page reload.

Assume we have a route defined in our router configuration like this:

```ts
{
    path: '/favorites',
    name: 'favorites',
    component: () => import('@/views/Favorites.vue'),
}
```

How to route to above path with RouterLink in the template?

```vue
<template>
    <div>
        <RouterLink :to="{ name: 'favorites' }">Go to Favorites</RouterLink>
    </div>
</template>
```

If there is parameter in the route such as:

```ts
{
    path: '/favorites/:id',
    //                ^^^
    name: 'favorites',
    component: () => import('@/views/Favorites.vue'),
}
```

Then you can pass the parameter like this:

```vue
<template>
    <div>
        <RouterLink :to="{ name: 'favorites', params: { id: 123 } }">Go to Favorites</RouterLink>
    </div>
</template>
```

Or you can just use the path directly:

```vue
<template>
    <div>
        <RouterLink :to="{ path: '/favorites/123' }">Go to Favorites</RouterLink>
    </div>
</template>
```

In fact, the `path` can be omitted if you just pass a string to `to`:

```vue
<template>
    <div>
        <RouterLink to="/favorites/123">Go to Favorites</RouterLink>
    </div>
</template>
```

