---
date: 2025-05-15
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Pinia Store Getter: return function

Sometimes in different situations, it is necessary to obtain a specific part of the store state. In these cases, we need to create an action that can take parameters and return the desired data.

```ts
import { defineStore } from 'pinia'

interface Recipe {
    id: number
    name: string
    ingredients: string[]
}

export const useRecipeStore = defineStore('recipe', {

    // state
    const recipes = ref<Recipe[]>([])
    
    // getters
    const numberOfRecipes = computed(() => recipes.value.length) // this is the first type of getter, in component we just simply use it

    const getRecipeById = (id: number) => { // this is the second type of getter, we can use it in component and wrap it in a computed callback function
        return recipes.value.find(recipe => recipe.id === id)
    }
})
```

Then, for this type of getter, we need to use it inside a computed callback function in the component.

```vue
<template>
    <div>
        <h1>Recipe</h1>
        <p>{{ recipe.name }}</p>
    </div>
</template>

<script setup lang="ts">
import { useRecipeStore } from '@/stores/recipe'

// assume that recipe id comes from the URL parameter `id`
const useRouter = useRoute()
const recipeId = computed(() => useRouter.params.id)

// Then, we can pass `id` to the getter `getRecipeById` to get the specific recipe
const recipeStore = useRecipeStore()
const recipe = computed(() => recipeStore.getRecipeById(recipeId.value))
//             ^^^^^^^^^ be note, we should use it inside a computed callback function to keep the reactivity
</script>
```

