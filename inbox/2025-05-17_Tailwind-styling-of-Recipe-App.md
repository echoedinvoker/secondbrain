---
date: 2025-05-17
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Tailwind styling of Recipe App

The following codes are mainly used to demonstrate how to use Tailwind CSS to beautify the interface of the Recipe App, so irrelevant parts will be omitted using `...`

```vue
<!--src/App.vue-->
<template>
  <header>
    <nav>
      <ul class="flex gap-4 p-8 bg-orange-200 text-orange-800">
        <li><RouterLink ... >Home</RouterLink></li>
        <li><RouterLink ... class="hover:underline">Favorites</RouterLink></li>
        <li><RouterLink ... class="hover:underline">Add Recipe</RouterLink></li>
      </ul>
    </nav>
  </header>

  <main class="bg-orange-100 h-screen p-4"> <!-- use <main> to wrap the main content, 'h-screen' to make 'bg-orange-100' fill the whole screen -->
    <RouterView />
  </main>
</template>
...
```

```vue
<!--src/views/HomeView.vue-->
<template>
  <h1 class="text-2xl font-bold mb-4">Recipe Book</h1>
  <div><input ... class="p-2 border rounded mb-4 w-full" /></div>
  <RecipeList :recipes="filteredRecipes" />
</template>
...
```

There are many other views in the app, so it's better to create a separate component for styling the header instead of copy pasting the same Tailwind classes in every view.

```vue
<!--src/components/RecipeList.vue-->
<template>
  <div>
    <nav ...>
      <ul>
        <li v-for="recipe in recipes" :key="recipe.id" class="mb-2">
          <RouterLink ... class="text-amber-600 hover:underline">{{ recipe.name }} </RouterLink>
        </li>
      </ul>
    </nav>

    <div ... class="text-gray-500">No recipes found!</div>
  </div>
</template>
...
```

In addition to HomeView and FavoritesView using the RecipeList component to display the recipe list, we also have some other views whose main component is a form used to add or modify recipe data. For example, AddRecipeView:

```vue
<!--src/views/AddRecipeView.vue-->
<template>
  <h1 class="text-2xl font-bold mb-4">Add form</h1>
  <form ... class="space-y-4">
    <div><input ... class="p-2 border rounded w-full" /></div>
    <div><textarea ... class="p-2 border rounded w-full"></textarea></div>
    <button ... class="px-4 py-2 bg-orange-600 text-white rounded hover:bg-orange-700">Add</button>
  </form>
</template>
```

The classes for input, textarea, and button above may be repeated in similar views, so the best practice is to refactor them into independent components and design their styling uniformly. This way, there is no need to repeat writing them in every view.
