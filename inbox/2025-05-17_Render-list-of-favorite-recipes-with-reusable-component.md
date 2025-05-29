---
date: 2025-05-17
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Render list of favorite recipes with reusable component

This is a real case scenario using the situation of refactoring reusable components and the use of store computed type getters.

```typescript
//src/stores/recipe.ts

import { defineStore } from 'pinia';
import { ref } from 'vue';

interface Recipe {
  id: string;
  name: string;
  description: string;
}

type NewRecipe = Omit<Recipe, 'id'>;

export const useRecipeStore = defineStore('recipe', () => {
  const recipes = ref<Recipe[]>([]);
  const favorites = ref<string[]>([]);

  const toggleFavorite = (id: string) => {
    if (favorites.value.includes(id)) {
      favorites.value = favorites.value.filter((favId) => favId !== id);
    } else {
      favorites.value.push(id);
    }
  };

  const isFavorite = (id: string) => favorites.value.includes(id);

  const addRecipe = (recipe: NewRecipe) => {
    const newRecipe = {
      id: Date.now().toString(),
      ...recipe
    };
    recipes.value.push(newRecipe);
    return newRecipe;
  };

  const editRecipe = (updatedRecipe: Recipe) => {
    const index = recipes.value.findIndex((recipe) => recipe.id === updatedRecipe.id);
    if (index !== -1) {
      recipes.value[index] = updatedRecipe;
    }
  };

  const getRecipeById = (id: string) => recipes.value.find((recipe) => recipe.id === id);

  const filteredRecipes = (searchQuery: string) =>
    recipes.value.filter((recipe) =>
      recipe.name.toLocaleLowerCase().includes(searchQuery.toLocaleLowerCase())
    );

  return {
    recipes,
    addRecipe,
    getRecipeById,
    filteredRecipes,
    editRecipe,
    favorites,
    toggleFavorite,
    isFavorite
  };
});
```

```vue
<!--src/views/HomeView.vue-->
<template>
  <main>
    <h1>Recipe Book</h1>
    <div>
      <input type="text" placeholder="Seach recipes..." v-model="searchQuery" />
    </div>
    <nav v-if="filteredRecipes.length > 0">
      <ul>
        <li v-for="recipe in filteredRecipes" :key="recipe.id">
          <RouterLink :to="{ name: 'recipe', params: { id: recipe.id } }">{{ recipe.name }}</RouterLink>
        </li>
      </ul>
    </nav>

    <div v-else>
      No recipes found!
    </div>
  </main>
</template>

<script setup lang="ts">
import { useRecipeStore } from '@/stores/recipe';
import { computed, ref } from 'vue';
import { RouterLink } from 'vue-router';

const recipeStore = useRecipeStore();
const searchQuery = ref('');
const filteredRecipes = computed(
  () => recipeStore.filteredRecipes(searchQuery.value)
);
</script>
```
Based on the background of the above codes, I want to write a new page to display a list of favorite recipes.

We can see that there is already a part displaying the recipe list in HomeView.vue. We can refactor this part into a reusable component specifically for displaying the recipe list. This way, we can use this component in both HomeView.vue and FavoriteRecipeList.vue to display either the recipe list or the favorite recipe list.


```vue
<!-- src/components/RecipeList.vue (new file) -->
<template>
  <div>
    <nav v-if="recipes.length > 0">
      <ul>
        <li v-for="recipe in recipes" :key="recipe.id">
          <RouterLink :to="{ name: 'recipe', params: { id: recipe.id } }">{{ recipe.name }}</RouterLink>
        </li>
      </ul>
    </nav>

    <div v-else>
      No recipes found!
    </div>
  </div>
</template>

<script setup lang="ts">
import type { Recipe } from '@/stores/recipe';
import { RouterLink } from 'vue-router';
defineProps<{
  recipes: Recipe[]
}>();
</script>
```

This component above does not directly retrieve data from the recipe store, but instead receives the data of recipes through props. This allows it to receive different recipe lists from different components, such as HomeView.vue and FavoriteRecipeList.vue. This logic makes it more reusable.

Next, we need to create a computedRef `favoriteRecipes` in recipe store to filter the favorite recipes from the recipe list with favorite ids.


```typescript
//src/stores/recipe.ts
import { defineStore } from 'pinia';
import { computed, ref } from 'vue';

export interface Recipe {
  id: string;
  name: string;
  description: string;
}

type NewRecipe = Omit<Recipe, 'id'>;

export const useRecipeStore = defineStore('recipe', () => {
  const recipes = ref<Recipe[]>([]);
  const favoriteIds = ref<string[]>([]);

  // ...

  // new getter (computedRef), we can directly compute the favorite recipes in the store because its content won't change by different components
  const favotiteRecipes = computed(() =>
    recipes.value.filter((recipe) => favoriteIds.value.includes(recipe.id))
  );

  return {
    // ...
    favotiteRecipes // expose the favorite recipes
  };
});
```

Finally, we can create a new page called `FavoritesView.vue` to display the list of favorite recipes. This page will use the `RecipeList` component we just created, passing the `favotiteRecipes` from the recipe store as a prop.


```vue
<!--src/views/FavoritesView.vue (new file) -->
<script setup lang="ts">
import RecipeList from '@/components/RecipeList.vue';
import { useRecipeStore } from '@/stores/recipe';
const store = useRecipeStore(); // import the recipe store
</script>

<template>
  <div>
    <h1>Favorite Recipes</h1>
    <RecipeList :recipes="store.favotiteRecipes" />
    <!--        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ passing the favorite recipes to the RecipeList component-->
  </div>
</template>
```
