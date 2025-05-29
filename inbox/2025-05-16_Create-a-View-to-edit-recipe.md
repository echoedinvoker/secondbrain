---
date: 2025-05-16
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Create a View to edit recipe

## Add action to edit recipe

```ts
import { defineStore } from 'pinia';
import { computed, ref } from 'vue';

export interface Recipe {
  id: string;
  name: string;
  description: string;
}

// ...

export const useRecipeStore = defineStore('recipe', () => {
  const recipes = ref<Recipe[]>([]);

  // ...

  const editRecipe = (updatedRecipe: Recipe) => {
    const index = recipes.value.findIndex((recipe) => recipe.id === updatedRecipe.id);
    if (index !== -1) { // -1 means method `findIndex` didn't find the recipe
      recipes.value[index] = updatedRecipe;
    }
  };

  const getRecipeById = (id: string) => recipes.value.find((recipe) => recipe.id === id);

  // ...

  return {
    recipes,
    addRecipe,
    getRecipeById,
    editRecipe,
    // ...
  };
});
```

## Create a view to edit recipe

```vue
<script setup lang="ts">
import { useRecipeStore } from '@/stores/recipe';
import { onMounted, ref } from 'vue';
import { useRoute, useRouter } from 'vue-router';
const store = useRecipeStore();
const router = useRouter();
const route = useRoute();

// local refs to constrol input and textarea
const name = ref('');
const description = ref('');

// fetch recipe by id from route params at component mount
const fetchRecipe = () => {
  const id = route.params.id as string;
  const recipe = store.getRecipeById(id);
  if (recipe) {
    name.value = recipe.name;
    description.value = recipe.description;
  } else {
    router.push({ name: 'not-found' })
  }
}
onMounted(fetchRecipe);

// function to update recipe with store action `editRecipe` and redirect to recipe page
const updateRecipe = () => {
  store.editRecipe({
    id: route.params.id as string,
    name: name.value,
    description: description.value
  });
  router.push({
    name: 'recipe',
    params: {
      id: route.params.id as string
    }
  })
}
</script>

<template>
  <h1 class="text-2xl font-bold mb-4">Edit form</h1>
  <form @submit.prevent="updateRecipe" class="space-y-4">
  <!--  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ bind to the function to update recipe -->
    <div>
      <input type="text" placeholder="Recipe name" v-model="name" required class="p-2 border rounded w-full" />
      <!--                                         ^^^^^^^^^^^^^^ -->
    </div>
    <div>
      <textarea v-model="description" placeholder="Recipe description" required class="p-2 border rounded w-full"></textarea>
      <!--      ^^^^^^^^^^^^^^^^^^^^^ -->
    </div>
    <button type="submit" class="px-4 py-2 bg-orange-600 text-white rounded hover:bg-orange-700">
      Edit
    </button>
  </form>
</template>
```

We also need to add a edit button to the recipe page to navigate to the edit page.

```vue
<template>
  <div>
    <h1 class="text-2xl font-bold mb-4">{{ recipe?.name }}</h1>
    <p class="mb-4">{{ recipe?.description }}</p>

    <!-- Add a button to route to the edit page of the recipe -->
    <div class="flex items-center gap-4">
      <RouterLink :to="{ name: 'edit-recipe', params: { id: recipe?.id } }" class="hover:underline">Edit</RouterLink>
    </div>

  </div>
</template>

<script setup lang="ts">
import { useRecipeStore } from '@/stores/recipe';
import { computed } from 'vue';
import { RouterLink, useRoute } from 'vue-router';

const route = useRoute();
const recipeStore = useRecipeStore();

const recipe = computed(() => recipeStore.getRecipeById(route.params.id as string));
const isFavorite = computed(() => (recipe.value ? recipeStore.isFavorite(recipe.value.id) : false));
</script>
```
