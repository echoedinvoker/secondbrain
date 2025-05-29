---
date: 2025-05-17
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Implement Favorite system of Recipe

Let's say you have a recipe app and you want to implement a favorite system for the recipes.

```ts
// src/stores/recipe.ts

// ...

export interface Recipe {
  id: string;
  name: string;
  description: string;
}

// ...

export const useRecipeStore = defineStore('recipe', () => {
  const recipes = ref<Recipe[]>([]);
  const favoriteIds = ref<string[]>([]); // add new ref for favorite IDs, it's recommended to store them in a separate ref instead of in the recipe object

  // new action to toggle recipe id to/out favoriteIds 
  const toggleFavorite = (id: string) => {
    if (favoriteIds.value.includes(id)) {
      favoriteIds.value = favoriteIds.value.filter((favId) => favId !== id);
    } else {
      favoriteIds.value.push(id);
    }
  };

  // new getter (return function type) to check if a recipe is favorite for specific id (so it'll be computed in the component instead of in the store)
  const isFavorite = (id: string) => favoriteIds.value.includes(id);

  // ...

  return {
    // ...
    favoriteIds,
    toggleFavorite,
    isFavorite,
  };
});
```

After implementing the store, you can use it in your component which is responsible for displaying the recipe details.

```vue
<!-- src/views/RecipeView.vue -->
<script setup lang="ts">

// ...

const recipeStore = useRecipeStore();

const recipe = computed(() => recipeStore.getRecipeById(route.params.id as string));

// because store getter is return function type, we need to computed with it in the component
const isFavorite = computed(() => (
  recipe.value // because recipe is also computed, we need to check if it's not null or undefined first
    ? recipeStore.isFavorite(recipe.value.id) // use getter return function of store to check if it's favorite
    : false
));

</script>

<template>

  <!-- ... other template code ... -->

      <button
        class="px-4 py-2 bg-orange-600 text-white rounded hover:bg-orange-700"
        @click="recipeStore.toggleFavorite(recipe.id)" <!-- bind click event to store action with recipe id -->
        v-if="recipe" <!-- because recipe has chance to be null or undefined, we need to use `v-if` to check if it's not null or undefined first" -->
                      <!-- or Typescript error wll be thrown on the `toggleFavorite(recipe.id)` -->
      >
        {{ isFavorite ? 'Remove from favorites' : 'Add to favorites' }} <!-- use computed value to show the button text conditionally -->
      </button>

  <!-- ... other template code ... -->

</template>

