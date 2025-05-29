---
date: 2025-05-15
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Pinia Store Getter example: computed in the store vs component
```ts
import { defineStore } from 'pinia'

interface Recipe {
  id: number
  name: string
  description: string
}

export const useRecipeStore = defineStore('recipe', () => {
  // state
  const recipes = ref<Recipe[]>([])
  const searchTerm = ref('')

  // getter
  const filteredRecipes = computed(() => recipes.value.filter(recipe => recipe.name.toLowerCase().includes(searchTerm.value.toLowerCase())))

  return {
    recipes,
    searchTerm,
    filteredRecipes
  }
})
```

```vue
<template>
  <div>
    <input v-model="recipeStore.searchTerm" placeholder="Search recipes..." />
    <ul>
      <li v-for="recipe in recipeStore.filteredRecipes" :key="recipe.id">
        {{ recipe.name }}: {{ recipe.description }}
    </ul>
  </div>
</template>
<script setup lang="ts">
import { useRecipeStore } from '@/stores/recipe'

const recipeStore = useRecipeStore()
</script>
```

In the example above, `searchTerm` is also defined in the store, so the getter `filteredRecipes` can be directly calculated in the store using `computed`. The component does not need to calculate it again, and can directly use `recipeStore.filteredRecipes`.

The advantage of this is that only one `filteredRecipes` are calculated in the store, and it is directly used in the component. This can reduce duplicate calculations and improve performance. However, the disadvantage is that this method is not suitable if different searches need to be performed in different components, because `searchTerm` is only one in the store.

So if you need to use the same `searchTerm` in multiple components, you move `searchTerm` to the component:

```javascript
import { defineStore } from 'pinia'

interface Recipe {
  id: number
  name: string
  description: string
}

export const useRecipeStore = defineStore('recipe', () => {
  // state
  const recipes = ref<Recipe[]>([])
  // remove searchTerm from the store to the component

  // getter
  const getFilteredRecipes = (searchTerm: string) => { // convert to a return function
  //    ^^^
    return recipes.value.filter(recipe => recipe.name.toLowerCase().includes(searchTerm.toLowerCase()))
  }

  return {
    recipes,
    getFilteredRecipes
  }
})
```

```vue
<template>
  <div>
    <input v-model="searchTerm" placeholder="Search recipes..." />
    <ul>
      <li v-for="recipe in recipeStore.filteredRecipes(searchTerm)" :key="recipe.id">
        {{ recipe.name }}: {{ recipe.description }}
      </li>
    </ul>
  </div>
</template>
<script setup lang="ts">
import { useRecipeStore } from '@/stores/recipe'
import { ref } from 'vue'

const recipeStore = useRecipeStore()
const searchTerm = ref('') // move searchTerm to the component
const filteredRecipes = computed(() => recipeStore.getFilteredRecipes(searchTerm.value))
//                      ^^^^^^^^^ computed locally                    ^^^^^^^^^^^^^^^^ call the getter function with the searchTerm
</script>
```

The above method is to change the definition of ref `searchTerm` in the component and also use `computed` in the component. The implication behind this is to perform calculations in the component instead of in the store. The store only provides functions and does not actually perform calculations.

The advantage of this method is that it can have multiple different `searchTerms` at the same time, each generating different `filteredRecipes`. The disadvantage is that if multiple components need to use the same `searchTerm`, there will be a problem of duplicate calculations.


## Conclusion

We need to decide which method to use based on actual needs.
