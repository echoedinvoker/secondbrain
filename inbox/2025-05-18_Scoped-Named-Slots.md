---
date: 2025-05-18
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Scoped Named Slots

```vue
<!--src/components/Child.vue-->
<script lang="ts" setup>
interface Item {
  id: number;
  name: string;
}
defineProps<{items: Item[]}>()
</script>

<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      <slot name="item" :item="item"></slot>
      <!--              ^^^^^^^^^^^^ this is the point that props data to slot -->
    </li>
  </ul>
</template>
```

The component above uses a named slot and passes data to the slot, making it a `scoped` named slot. The data passed to the slot can be used in the parent component to determine how the slot content is presented.

We can see that the slot has no default content or style, only passing data to the slot so that the parent can fully control the content and style of the slot. This pattern is called a render less component, which is a very powerful pattern.

```vue
<!--src/components/Parent.vue-->
<script lang="ts" setup>
import {ref} from 'vue'
import Child from '@/components/Child.vue'
const items = ref([
  {id: 1, name: 'Item 1'},
  {id: 2, name: 'Item 2'},
  {id: 3, name: 'Item 3'}
])
</script>

<template>
  <div>
    <h1>Item List</h1>
    <Child :items="items">
    <!--   ^^^^^^^^^^^^^^ pass data to child component -->
      <template #item="{item}">
      <!--      ^^^^^^^^^^^^^^ get data from child component and use it to design how to show the slot content in the parent component -->

        <strong class="text-5xl">{{ item.name }}</strong> <!-- this is the slot content, we write it in the parent component -->

      </template>
    </Child>
  </div>
</template>
```

Note that the data passed from the parent component to the child component is `items`, while the data passed from the child component to the parent component is `item`. These are different but related data. This is the essence of the render less component pattern, which encapsulates the data processing in the child component, allowing the parent component to focus on UI design. This design can make the parent component more concise and the child component more versatile, as it does not need to know what the UI design of the parent component looks like, it just needs to focus on processing the data.


## Variants

**source of child data**

The data of the child component can also be obtained not only from props, but also from other sources, such as fetching data from an API or getting data from a store.

**multiple named slots**

Of course, child components can also have multiple named slots to meet different needs.


## Not only for Vue

Render less component pattern is not only for Vue, but also for other frameworks. For example, in React, we can use `children` to achieve similar functionality. In Svelte, we can use `slot` to achieve similar functionality. In Angular, we can use `ng-template` to achieve similar functionality. In fact, this pattern is very common in many frameworks and libraries.
