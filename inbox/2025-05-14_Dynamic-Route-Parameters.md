---
date: 2025-05-14
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Dynamic Route Parameters

We can use add a segment to the URL that will be replaced with a dynamic value. This is done by adding a colon `:` before the segment name in the route definition.

```javascript
const router = createRouter({
    routes: [
        {
            path: '/user/:id',
            //           ^^^ dynamic segment
            component: User,
        },
        ...
    ],
});
```

Then we can access the dynamic value in the component:

```vue
<template>
    <div>
        <h1>User {{ $route.params.id }}</h1>
        <!--        ^^^^^^^^^^^^^^^^ to access the dynamic segment `id` value -->
    </div>
</template>

<script>
import { useRoute } from 'vue-router';

const route = useRoute(); // useRoute is a Composition API function to access the current route

console.log(route.params.id);
//          ^^^^^^^^^^^^^^^ to access the dynamic segment `id` value, don't need `$` prefix here
</script>
```
