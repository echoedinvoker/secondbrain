---
date: 2025-05-14
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Route programmatically

Someitmes instead of using the `RouteLink` component, you may want to programmatically navigate to a different route. You can do this using the `useRouter` hook.

Assume we want to navigate to below route:

```javascript
{
    path: '/recipes/:id',
    name: 'recipe',
    component: RecipeView
}
```

Then we can use the `useRouter` hook to navigate to this route programmatically. Here is an example of how to do this:

```javascript
import { useRouter } from 'vue-router';

const router = useRouter();
const addRecipe = () => {
    router.push({ name: 'recipe', params: { id: 123 } });
};
```

Instead of `name`, you can also use `path` to navigate to the route. Here is an example of how to do this:

```javascript
import { useRouter } from 'vue-router';

const router = useRouter();
const addRecipe = () => {
    router.push({ path: '/recipes/123' }); // be note that the `params` shouldn't be used if you use `path`
};
```

