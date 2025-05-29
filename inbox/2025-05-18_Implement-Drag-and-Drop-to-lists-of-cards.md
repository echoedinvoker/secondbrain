---
date: 2025-05-18
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Implement Drag and Drop to lists of cards

```vue
<!--src/App.vue-->
<script setup lang="ts">
import {reactive} from 'vue'

interface Card {
  id: number;
  title: string;
  description: string;
}

interface List {
  id: number;
  title: string;
  cards: Card[];
}

const lists = reactive<List[]>([
//            ^^^^^^^^ for the nested structure, reactive has better performance than ref
  {
    id: 1,
    title: 'To Do',
    cards: [
      { id: 1, title: 'Task 1', description: 'Description for Task 1' },
      { id: 2, title: 'Task 2', description: 'Description for Task 2' }
    ]
  },
  {
    id: 2,
    title: 'In Progress',
    cards: [
      { id: 3, title: 'Task 3', description: 'Description for Task 3' },
      { id: 4, title: 'Task 4', description: 'Description for Task 4' }
    ]
  },
  {
    id: 3,
    title: 'Done',
    cards: [{ id: 5, title: 'Task 5', description: 'Description for Task 5' }]
  }
])
</script>

<template>
  <main class="p-5 font-sans">
    <div class="flex gap-5 py-5 overflow-x-auto">
      <div class="bg-gray-100 p-3 rounded-lg min-w-[250px] flex flex-col" v-for="list in lists" :key="list.id">
        <h2 class="font-medium mb-2">{{ list.title }}</h2>
        <div class="bg-white p-2 my-2 rounded shadow cursor-pointer" v-for="card in list.cards" :key="card.id">
          <span class="text-sm font-medium">{{ card.title }}</span>
          <p class="text-xs text-gray-400">
            {{ card.description }}
          </p>
        </div>

        <button class="w-full bg-transparent rounded hover:bg-white text-gray-500 p-2 text-left mt-2 text-sm font-medium">
          + Add Card
        </button>
      </div>
    </div>
  </main>
</template>
```

According to the nested data structure of lists, the above component will draw three lists, each list containing several cards. If you want the cards to be draggable within the same list and between different lists, we have the following two options:

1. Use library 'vue-draggable-next' (recommended, very simple)
2. Use HTML5 drag and drop API (not recommended, only used when your requirements cannot be met by the library)

Here we choose to use the 'vue-draggable-next' library to implement drag and drop functionality, because if we use the HTML5 drag and drop API, the code will be approximately 2 to 3 times larger.
```vue
<!--src/App.vue-->
<script setup lang="ts">
import Draggable from 'vuedraggable' // import the library, its default export, so we can name it anything
//  ... same as before
</script>

<template>
  <main class="p-5 font-sans">
    <div class="flex gap-5 py-5 overflow-x-auto">
      <div class="bg-gray-100 p-3 rounded-lg min-w-[250px] flex flex-col" v-for="list in lists" :key="list.id">
        <h2 class="font-medium mb-2">{{ list.title }}</h2>

        <!-- replace cards with Draggable component -->
        <Draggable :list="list.cards" group="cards">
        <!--       ^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^ cards only can drop in the same group, so here all cards are in the same group, which means we can drag and drop between lists -->
        <!--       ^^^^^^^^^^^^^^^^^^ instead of v-for, we use the list attribute to bind the cards array to the Draggable component -->

          <!-- this is named scope slot, which is a special slot that accepts a prop from the parent component: #[slot name]="prop" -->
          <template #item="{element}">
          <!--             ^^^^^^^^^ destructuring the 'element' prop from the props object, which is the current card object -->

            <!-- this is the same as before, but now we replace 'card' with 'element' -->
            <div class="bg-white p-2 my-2 rounded shadow cursor-pointer">
              <span class="text-sm font-medium">{{ element.title }}</span>
              <!--                                 ^^^^^^^         -->
              <p class="text-xs text-gray-400">
                {{ element.description }}
              <--  ^^^^^^^ -->
              </p>
            </div>
          </template>
        </Draggable>

        <button class="w-full bg-transparent rounded hover:bg-white text-gray-500 p-2 text-left mt-2 text-sm font-medium">
          + Add Card
        </button>
      </div>
    </div>
  </main>
</template>
```

Now the cards can be dragged and dropped between lists or within the same list. The `Draggable` component takes care of the drag and drop functionality, and we just need to provide it with the list of cards and the group name.

